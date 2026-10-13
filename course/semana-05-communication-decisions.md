# Semana 5. Decisiones de comunicación de SkillHub

## Criterio general

La elección depende de si el productor necesita el resultado para continuar, de cuánto puede durar el trabajo y de qué ocurre si el consumidor está lento o indisponible. Los contratos descritos aquí son conceptuales: no definen endpoints, esquemas completos, proveedores ni topología de despliegue.

| Flujo | Productor | Consumidor | Decisión de comunicación | Cola, Pub/Sub, evento o webhook | Timeout / retención | Reintentos e idempotencia |
|---|---|---|---|---|---|---|
| Consulta de datos del empleado para validar una asignación | SkillHub, al preparar o revisar una asignación | Sistema corporativo de empleados | **Síncrona, REST orientada a recurso.** SkillHub necesita saber si la persona existe, está activa y cumple área o rol antes de activar una nueva asignación. | No usar cola para la consulta interactiva: ocultaría una decisión que RR. HH. necesita conocer. No es un evento ni webhook porque se solicita el estado actual. | Objetivo provisional: p95 <= 2 s; timeout de 3 s. En campañas, las validaciones no resueltas se conservan como pendientes. | La lectura es naturalmente idempotente. Permitir un reintento ante timeout o error transitorio; no repetir ante respuesta negativa válida o saturación persistente. La activación local usa la clave empleado-curso-campaña para evitar duplicados. |
| Notificación de una asignación o certificado | M-02 Asignaciones o M-04 Certificación, cuando ocurre el hecho | M-06 Comunicación y bandeja; en el futuro, auditoría u otros consumidores | **Asíncrona mediante evento.** El productor no debe esperar al correo para confirmar una asignación o certificado. | El hecho `AsignacionCreada` o `CertificadoEmitido` puede publicarse en **Pub/Sub** porque varios consumidores pueden reaccionar sin que el productor los conozca. Cada entrega al canal de comunicación se procesa mediante una **cola**, que distribuye el trabajo a un consumidor y permite reintentos. No es un comando: informa algo ya ocurrido. | El productor espera solo confirmación breve de aceptación del evento, provisionalmente <= 2 s. Retención de mensajes no procesados: pendiente de validar; hipótesis inicial de 24 h. | El evento debe tener identificador único. Comunicación deduplica por evento + destinatario + canal + tipo de notificación. Reintenta fallos transitorios con límite; un mensaje inválido pasa a revisión, no se reintenta indefinidamente. |
| Generación de reporte inicial de capacitación | RR. HH., al solicitar filtros de cobertura, finalización o resultados | M-07 Reportes y auditoría, que genera el resultado y lo deja disponible | **Asíncrona mediante un comando de generación.** El reporte puede combinar muchos registros y no debe mantener abierta una interacción ni bloquear otras operaciones. La solicitud puede entrar por una interfaz REST orientada al recurso “solicitud de reporte”, pero su procesamiento posterior es asíncrono. | `GenerarReporteSolicitado` es un **comando**: pide una acción a un consumidor. Se coloca en una cola para absorber picos y distribuir trabajos. Al terminar, Reportes publica `ReporteGenerado` como evento para la bandeja o Comunicación. Pub/Sub solo es necesario si varios consumidores necesitan el hecho. | Timeout de aceptación de la solicitud: provisionalmente <= 2 s. El trabajo puede tardar hasta 60 s como hipótesis inicial; la solicitud y el resultado se conservan 7 días, pendiente de validar. | La solicitud lleva un identificador de operación y una clave de idempotencia. Repetir la misma solicitud con los mismos filtros devuelve el trabajo existente; cambiar filtros requiere otra clave. El consumidor confirma la cola solo después de persistir resultado o estado de error final. |

## 1. Consulta síncrona del sistema de empleados

### Razón de la elección

La elegibilidad es una condición para activar una asignación nueva. SkillHub necesita una respuesta antes de confirmar el estado `Activa`; por tanto, la comunicación síncrona ofrece una decisión clara en el flujo actual. REST es suficiente porque la operación consulta una representación de un empleado y favorece interoperabilidad con un sistema corporativo externo. RPC solo sería preferible si el contrato interno necesitara una operación de dominio muy específica y ambas partes controlaran estrechamente el protocolo.

La dependencia aumenta el acoplamiento temporal: si el sistema externo está lento, la experiencia de RR. HH. también lo nota. Ese costo es aceptable para esta regla porque usar datos viejos podría asignar capacitación a un empleado inactivo o fuera del área o rol objetivo. Si no hay respuesta, la asignación queda pendiente, tal como se definió en el análisis de la semana 4.

### Contrato mínimo

- **Solicitud:** identificador del empleado y criterio que debe comprobarse, sin permitir que SkillHub envíe una modificación de datos maestros.
- **Respuesta positiva:** identificador, existencia, estado activo, área, rol y momento o versión de observación.
- **Respuesta negativa:** empleado inexistente, inactivo o no elegible, con razón distinguible.
- **Errores:** timeout, indisponibilidad, respuesta incompleta, contrato incompatible y saturación; deben distinguirse de una negativa válida.
- **Compatibilidad:** agregar campos opcionales no rompe consumidores; no se debe cambiar el significado de un campo existente sin una transición acordada. La respuesta debe permitir detectar datos desactualizados.

Ante exceso de solicitudes, SkillHub debe limitar la campaña, espaciar consultas o dejar elementos pendientes. Reintentar sin límite convertiría una falla del proveedor en una carga mayor y podría empeorar el tiempo de recuperación.

## 2. Notificación de una asignación o certificado

### Razón de la elección

La entrega del correo no es una condición para crear la asignación ni para emitir el certificado. Un evento permite separar el hecho de negocio de su comunicación: el productor publica que algo ocurrió y Comunicación decide cómo preparar la bandeja interna y el correo. Esto reduce acoplamiento y permite que la operación principal sobreviva a una indisponibilidad del correo.

Pub/Sub y cola cumplen funciones diferentes:

- **Pub/Sub:** distribuye el mismo hecho a varios suscriptores independientes, por ejemplo Comunicación y Auditoría. El productor no necesita conocerlos individualmente.
- **Cola:** entrega una tarea a un consumidor competidor, espera confirmación de procesamiento y permite absorber picos o reintentar. La cola es apropiada para cada intento de preparar y entregar una notificación.

El evento no ordena “envía un correo”; informa `AsignacionCreada` o `CertificadoEmitido`. La solicitud interna de entrega sí es un comando derivado, pero no debe cambiar el resultado original.

### Contrato mínimo

- **Evento:** identificador único, tipo de hecho, versión del contrato, fecha del hecho, empleado destinatario, curso, referencia a la asignación o certificado y contexto mínimo para redactar el aviso.
- **Errores:** evento duplicado, campo obligatorio ausente, versión no compatible, canal no disponible y rechazo temporal del consumidor.
- **Entrega:** estado `Pendiente`, `Entregada`, `Fallida` o `No procesable`, con motivo y número de intentos.
- **Compatibilidad:** los consumidores ignoran campos desconocidos cuando sea seguro; no se reutiliza el mismo tipo de evento para cambiar su significado; un cambio semántico requiere un nuevo tipo o versión compatible.

El productor espera solo que el evento haya sido aceptado de forma durable según el contrato. Si no recibe esa confirmación, puede repetir la publicación con el mismo identificador. Comunicación debe deduplicar, porque una respuesta perdida puede ocultar que el evento ya fue aceptado. Los fallos temporales se reintentan con límite; los permanentes se conservan para revisión y no bloquean la asignación ni el certificado.

## 3. Generación de un reporte inicial

### Razón de la elección

Un reporte combina asignaciones, progreso, resultados y datos organizacionales con filtros variables. La duración depende del periodo y de la concurrencia de RR. HH.; no es necesario que quien solicita espere el documento final para continuar. Una cola desacopla la solicitud de la generación, absorbe campañas de consulta y evita que reportes pesados degraden las operaciones interactivas.

La solicitud es un comando porque pide producir un resultado. `ReporteGenerado` es un evento porque comunica que el resultado ya existe. La distinción evita que el consumidor trate un hecho como una instrucción o que el productor tenga que conocer quién usará el reporte.

### Contrato mínimo

- **Comando:** identificador de solicitud, actor autorizado, filtros de curso, área, rol y periodo, formato solicitado, fecha de creación y prioridad permitida.
- **Resultado:** identificador de reporte, filtros aplicados, fecha de corte de los datos, estado, referencia al resultado y advertencias sobre datos pendientes o desactualizados.
- **Errores:** filtros inválidos, actor sin permisos, datos incompletos, cancelación, exceso de solicitudes y fallo interno de generación.
- **Compatibilidad:** los filtros nuevos deben ser opcionales; los consumidores deben tolerar estados desconocidos sin asumir que significan `Completado`; la semántica de la fecha de corte debe permanecer estable.

Si la cola está llena o el límite de solicitudes se excede, SkillHub debe rechazar la nueva solicitud con una indicación explícita o dejarla en estado pendiente según una cuota acordada. No debe crear múltiples trabajos equivalentes por cada reintento del usuario. Si el consumidor falla, el mensaje vuelve a estar disponible dentro de la retención; después del límite, queda en error visible para intervención.

## 4. Supuestos y preguntas pendientes

1. ¿El sistema corporativo de empleados ofrece una consulta de lectura con latencia y disponibilidad suficientes para el objetivo provisional de p95 de 2 segundos y timeout de 3 segundos?
2. ¿Existe un mecanismo corporativo capaz de retener, reintentar y deduplicar eventos y comandos, o debe limitarse inicialmente la comunicación asíncrona a un alcance más pequeño?
3. ¿Qué volumen y duración máximos tendrán los reportes, y qué periodo debe conservarse un resultado antes de regenerarlo?
4. ¿RR. HH. necesita que la bandeja interna sea siempre inmediata, o acepta que una notificación aparezca después durante una indisponibilidad del correo?