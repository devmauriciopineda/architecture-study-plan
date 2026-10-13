# Semana 8. Objetivos de resiliencia de SkillHub

## 1. Vocabulario y alcance

- **Disponibilidad:** proporción de solicitudes o tiempo en que el servicio acordado está utilizable.
- **Confiabilidad:** capacidad de funcionar correctamente, conservando resultados, permisos e invariantes durante un periodo.
- **Resiliencia:** capacidad de absorber una perturbación, degradar de forma controlada y recuperar una condición conocida.
- **Tolerancia a fallos:** capacidad de continuar una operación cuando falla una parte, sin necesariamente mantener todas las funciones.

La política protege como servicio principal las operaciones interactivas de SkillHub: autenticación, consulta de asignaciones y cursos, registro de progreso, presentación de evaluaciones y consulta de certificados. El correo, los reportes pesados y algunos documentos pueden quedar pendientes o degradados, pero la degradación no puede violar permisos, integridad de evaluaciones, ownership de datos ni auditoría.

## 2. Escenarios de fallo y respuesta

| Escenario y dominio de fallo | Health check y detección | Timeout y reintentos | Circuit breaker | Degradación permitida | Redundancia o recuperación | Efecto sobre datos y usuarios |
|---|---|---|---|---|---|---|
| **Sistema corporativo de empleados indisponible.** Dominio: dependencia externa y red entre organizaciones. | Separar vida del proceso de disponibilidad real. Usar una comprobación de preparación que mida una consulta de lectura controlada, latencia y errores, con límites para no agravar la carga. Medir tasa de respuestas válidas, timeout y antigüedad de la última sincronización. | Timeout provisional de 3 s por validación, coherente con semana 4. Un reintento solo ante fallo transitorio, con espera limitada. No reintentar respuestas negativas, inválidas o saturación persistente. | Abrir después de errores o latencia sostenidos; fallar rápido y pasar nuevas validaciones a `Pendiente`. Probar de forma limitada el cierre del circuito. | Permitir consultar asignaciones ya confirmadas y registrar nuevas solicitudes como pendientes. No usar la copia local vieja para activar asignaciones, autorizar resultados sensibles o aceptar cambios de rol. | SkillHub no puede crear redundancia del sistema propietario. Mantener una representación local para lectura informativa y reportes, y reintentar tras recuperación del proveedor. La restauración de la fuente externa corresponde a su propietario. | No se modifican datos maestros. Nuevas asignaciones pueden retrasarse; las existentes permanecen. RR. HH. ve causa, hora y cantidad de pendientes. |
| **Fallo de entrega de notificaciones.** Dominio: cola de trabajos, consumidor de comunicación o correo corporativo. | Comprobar que la cola acepta trabajo, que la edad del mensaje está dentro del objetivo y que un envío de prueba controlado obtiene un estado. Medir profundidad, edad máxima, entregas, reintentos y mensajes no procesables. | Timeout por intento de entrega de aproximadamente 3 s como hipótesis. Reintentos limitados con espera creciente; no repetir indefinidamente ni reintentar mensajes inválidos. | Abrir para el canal que acumule fallos o latencia; detener temporalmente llamadas al correo y dejar trabajo en cola. El circuito de la notificación no debe bloquear la asignación o certificado. | Mantener la bandeja interna y confirmar la operación principal. El correo puede quedar `Pendiente` o `Fallido`; no se presenta como entregado. | Cola durable, deduplicación por evento-destinatario-canal-tipo y reprocesamiento controlado. Si se pierde la cola, reconstruir solicitudes desde eventos de negocio o auditoría cuando estén disponibles; esto requiere una prueba de reconstrucción. | No se duplica una asignación ni un certificado. El usuario puede ver el estado interno y el retraso; la información sensible solo aparece a usuarios autorizados. |
| **Pérdida o indisponibilidad del almacenamiento de documentos.** Dominio: almacenamiento de contenidos, adjuntos y archivos de certificados. | Comprobar lectura y escritura de un objeto de prueba no sensible, capacidad de respuesta y coincidencia de integridad. Distinguir proceso activo de almacenamiento preparado. Medir errores de lectura, escritura, integridad y edad del último respaldo verificable. | Timeout de lectura/escritura provisional de 5 s. Reintentar solo operaciones seguras o identificadas de forma idempotente; no repetir una escritura incierta sin poder reconocer el mismo documento. | Abrir si el almacenamiento falla de forma sostenida para evitar saturar conexiones y ocultar el problema. Cerrar después de lecturas y escrituras de prueba exitosas. | Mostrar metadatos y estado `No disponible`; permitir navegar por cursos que no necesiten el documento. No permitir completar contenido obligatorio inaccesible ni afirmar que un certificado descargó correctamente. | Copias de respaldo y al menos una copia recuperable fuera del mismo dominio de fallo, según RPO/RTO validados. La pérdida por corrupción exige restauración y comprobación de integridad, no solo retirar tráfico. | No se pierde el estado editorial, progreso o certificado si sus referencias permanecen; el archivo puede quedar pendiente de restauración. Usuarios reciben un error claro y no deben reintentar creando documentos duplicados. |
| **Caída de la aplicación durante una evaluación.** Dominio: proceso o instancia de SkillHub; puede afectar sesión, conexión y respuesta al usuario. | Comprobación de vida para detectar proceso caído y preparación para aceptar tráfico solo cuando puede leer estado operativo. Medir errores de evaluación, operaciones sin confirmación y tiempo hasta reanudar. | Guardar progreso puede reintentarse si es idempotente. El envío final usa un identificador de operación y timeout finito, provisionalmente 3 s; si la respuesta se pierde, consultar el estado antes de repetir. | No es el mecanismo principal para una caída de la propia aplicación; retirar la instancia no preparada y evitar enviarle tráfico. Usar circuit breaker para dependencias externas invocadas durante la evaluación. | Permitir reanudar desde el último progreso confirmado. No consumir otro intento ni marcar aprobado/reprobado hasta confirmar el resultado. Si la evaluación no puede cerrarse, dejarla pendiente y comunicarlo. | Instancias redundantes o reinicio controlado pueden recuperar el proceso; el estado debe estar fuera de la memoria de una instancia. Restaurar datos solo si hubo corrupción o pérdida, siguiendo RTO/RPO. | No se aceptan respuestas parciales como intento completo. Un envío confirmado una sola vez produce un resultado único; un envío incierto se puede consultar y resolver sin duplicar intento. El usuario ve estado pendiente o reanudable. |

## 3. Fallos aislables frente a recuperación ante desastres

Los fallos del correo, una instancia de aplicación y una consulta externa lenta pueden aislarse mediante timeouts, circuit breakers, cola, retiro de tráfico y estados pendientes. En estos casos, el servicio principal puede seguir atendiendo una parte útil sin inventar resultados.

La pérdida o corrupción del almacenamiento de documentos y la pérdida general del estado operativo superan la capacidad de un simple reintento. Requieren recuperación ante desastres: copias recuperables, responsables, orden de restauración, verificación de integridad y una decisión sobre qué datos pueden perderse según el RPO. Una copia que nunca se restaura no demuestra resiliencia.

La caída de la aplicación durante una evaluación necesita primero recuperación operativa y protección de la transacción. Solo se convierte en desastre si la caída implica pérdida o corrupción del estado que no puede recuperarse dentro del objetivo. La separación es importante: reiniciar un proceso no justifica restaurar una copia antigua ni duplicar intentos.

## 4. SLI, SLO, SLA, RTO y RPO provisionales

### Servicio principal

| Elemento | Propuesta provisional | Justificación y medición |
|---|---|---|
| **SLI de disponibilidad** | Porcentaje de solicitudes válidas del servicio principal que terminan con una respuesta correcta dentro del timeout, excluyendo rechazos intencionales por falta de autorización pero contabilizando errores del sistema. | Medir por operación y ventana mensual: catálogo, asignaciones, progreso, evaluación y certificados. Una respuesta rápida con datos incorrectos no cuenta como éxito. |
| **SLI de latencia** | Percentil 95 de solicitudes interactivas completadas. | Evita ocultar esperas largas tras un promedio; se separan operaciones críticas de reportes y notificaciones asíncronas. |
| **SLO provisional** | >= 99,5% de solicitudes válidas del servicio principal correctas durante el horario operativo mensual; p95 <= 3 s para operaciones interactivas, fuera de una indisponibilidad declarada del sistema corporativo de empleados. | Es un objetivo inicial para una plataforma interna de 1.000 empleados, no un compromiso definitivo. Las excepciones deben ser visibles y no convertir fallos de una dependencia en éxitos falsos. |
| **RTO provisional** | Restaurar el servicio principal en <= 2 horas después de una pérdida relevante de aplicación o datos, sujeto a validación operativa. | La capacitación es importante para cumplimiento, pero el contexto actual permite recuperación manual. El valor debe comprobarse con un ejercicio y disponibilidad de personal. |
| **RPO provisional** | Perder como máximo 4 horas de cambios confirmados, sujeto a validación. | Evaluaciones, resultados y certificados son evidencia; perder un día completo por depender solo de respaldos diarios puede ser demasiado costoso. El requisito actual de respaldo diario no demuestra todavía este RPO y deja una brecha que debe resolverse o aceptarse explícitamente. |
| **SLA** | No proponer todavía un SLA externo; el SLO es una meta operativa interna. | Un SLA requeriría acordar horario, exclusiones, responsabilidades de TI y del sistema de empleados, medición y consecuencias. No todo SLO debe convertirse automáticamente en obligación contractual. |

El SLI mide, el SLO establece la meta y el SLA formaliza un compromiso entre partes. RTO y RPO describen recuperación y pérdida admisible; no sustituyen el SLO de solicitudes durante la operación normal.

## 5. Invariantes durante la degradación

- Un usuario nunca obtiene acceso a resultados, certificados o reportes fuera de su autorización.
- Una evaluación incierta no consume un intento adicional ni se convierte en aprobada o reprobada sin resultado confirmado.
- La indisponibilidad del sistema de empleados no permite editar datos maestros ni activar una asignación con información vieja no verificada.
- Un correo pendiente no revierte una asignación, finalización o certificado, y un reintento no duplica sus efectos.
- Cada degradación comunica su estado, causa y fecha de actualización cuando la información puede estar incompleta o retrasada.
- Toda recuperación conserva trazabilidad de qué datos se restauraron, desde qué punto y qué pérdida fue aceptada.

## 6. Cómo comprobar que la recuperación funciona

1. Definir previamente el escenario, el dominio de fallo, el último estado confirmado, el RTO, el RPO y las invariantes que deben conservarse.
2. Ejecutar ejercicios controlados en un entorno separado o con una ventana aprobada, simulando cada pérdida sin modificar datos productivos no autorizados.
3. Restaurar aplicación, estado operativo, documentos y dependencias en el orden documentado, midiendo tiempo real y punto de recuperación alcanzado.
4. Verificar funcionalmente: un empleado puede consultar su asignación, una evaluación no duplica intentos, un certificado conserva su vínculo y los permisos siguen aplicándose.
5. Comparar registros antes y después, contar datos perdidos o duplicados y comprobar que reportes y auditoría reflejan la fecha de corte correcta.
6. Repetir el ejercicio periódicamente y después de cambios relevantes; registrar fallos del procedimiento, no solo el resultado exitoso.

## 7. Preguntas pendientes

1. ¿Qué RTO y RPO acepta la dirección para evaluaciones, resultados, certificados y documentos, y cambia el objetivo por horario laboral o periodo de campaña?
2. ¿Los respaldos diarios actuales son suficientes para el RPO acordado, o se necesita una frecuencia distinta y una copia fuera del dominio de fallo?
3. ¿Cuál es el horario operativo exacto, qué mantenimientos se excluyen del SLO y quién está disponible para ejecutar una recuperación fuera de horario?
4. ¿Qué parte del sistema corporativo de empleados y del correo queda cubierta por acuerdos de disponibilidad, y qué ocurre si sus propios objetivos son peores que los de SkillHub?