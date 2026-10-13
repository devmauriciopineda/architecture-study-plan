# Semana 12. Ruta evolutiva de notificaciones de SkillHub

## 1. Propósito y alcance

Esta hoja de ruta define una evolución incremental para la capacidad de notificaciones de SkillHub. Incluye avisos de asignaciones, resultados relevantes, certificados y decisiones de revisión, entregados mediante bandeja interna y correo corporativo.

El objetivo no es migrar todo SkillHub ni adoptar una plataforma por anticipado. Es preparar una posible separación de M-06 Comunicación y bandeja sin interrumpir el MVP, sin cambiar las reglas de negocio y sin crear dos fuentes maestras permanentes.

La ruta tiene tres etapas condicionadas. Una etapa solo empieza cuando la anterior tiene métricas, datos y recuperación verificables. Si una condición no se cumple, se conserva el estado actual y se corrige el problema; no se avanza por calendario.

## 2. Estado actual resumido

### Arquitectura

- SkillHub se plantea como un monolito modular provisional.
- M-06 Comunicación y bandeja posee notificaciones, destinatario, canal, estado de entrega, reintentos y lectura interna.
- Asignaciones, aprendizaje y certificación son propietarios de los hechos de negocio que originan avisos.
- El correo corporativo es una dependencia externa; una falla de correo no debe revertir una asignación, un resultado ni un certificado.
- El sistema corporativo de empleados es la fuente maestra de identidad organizacional y autorización contextual.

### Capacidad operativa conocida

- La población inicial es de aproximadamente 1.000 empleados y hasta 100 cursos.
- Una campaña podría generar cerca de 1.000 notificaciones en una ráfaga, pero la cifra aún es una hipótesis.
- Se necesitan estados explícitos, deduplicación, reintentos limitados, backpressure y recuperación comprobable.
- El MVP debe poder desplegarse on-premise.
- El ADR-011 propone una cola durable interna y workers separados lógicamente de la ruta interactiva, pero permanece propuesto hasta validar correo, carga y restauración.

### Propiedades que no deben romperse

1. Una notificación no crea ni modifica el hecho de negocio que la originó.
2. Un usuario solo consulta notificaciones autorizadas para su identidad y alcance.
3. Un reintento no duplica una entrega lógica ni produce un segundo efecto de negocio.
4. Un correo pendiente o fallido es visible como tal y no se presenta como entregado.
5. Los eventos, estados y auditoría pueden rastrearse hasta su origen y fecha de corte.

## 3. Estrategia general de evolución

La secuencia elegida es:

1. **Monolito modular:** consolidar ownership, contratos y persistencia de M-06 dentro del despliegue existente.
2. **Strangler pattern:** introducir una fachada de notificaciones que pueda dirigir cada flujo al camino legado o al nuevo worker, manteniendo consumidores antiguos durante la transición.
3. **Extracción condicional:** convertir M-06 en un servicio separado solo si el rendimiento, el aislamiento de fallos, el ritmo de despliegue o una necesidad corporativa compartida justifican la operación distribuida.

La mejora de monolito a monolito modular es la opción por defecto porque reduce acoplamiento sin agregar red ni consistencia eventual. El patrón estrangulador es adecuado para mover el procesamiento gradualmente, siempre que la fachada no se convierta en una segunda fuente de reglas. La extracción a servicio es una opción posterior, no un objetivo obligatorio.

## 4. Etapas de la ruta

### Etapa 1. Consolidar M-06 dentro del monolito modular

**Objetivo**

Hacer que M-06 sea un límite real dentro del monolito: ownership explícito, contrato estable, estados de entrega y procesamiento asíncrono aislado de las operaciones interactivas.

**Cambio de ownership**

- M-06 pasa a ser la única fuente maestra del estado de notificación, bandeja, canal, reintentos y lectura.
- M-02, M-03 y M-04 conservan la propiedad de asignaciones, resultados y certificados.
- Los productores publican hechos o solicitudes; no escriben directamente tablas o estados internos de M-06.

**Compatibilidad necesaria**

- Las llamadas internas existentes que soliciten una notificación deben seguir funcionando mediante un contrato de fachada.
- Agregar campos opcionales no debe romper consumidores antiguos.
- Los estados anteriores deben mapearse a `Pendiente`, `Entregada`, `Fallida` o `No procesable` sin cambiar el significado de las decisiones de negocio.
- El adaptador de correo debe ocultar formato, autenticación y errores del proveedor corporativo.

**Datos migrados o duplicados**

- Migrar cualquier registro de notificación existente al ownership de M-06, conservando identificador, destinatario, evento de origen, canal, estado, intentos y fechas.
- Durante una ventana limitada puede existir una copia de comparación o una tabla de staging, pero no puede aceptar escrituras independientes.
- Registrar una clave estable de deduplicación para eventos ya procesados; los duplicados históricos deben marcarse, no borrarse sin auditoría.

**Mecanismo de observación**

- Medir p95 de aceptación, profundidad y edad máxima de cola, entregas, rebotes, reintentos, mensajes no procesables y duplicados.
- Correlacionar cada notificación con el evento de asignación, resultado, certificado o decisión.
- Comparar latencia de operaciones interactivas durante campañas con una línea base sin campaña.
- Ejecutar una prueba de pérdida de correo y verificar que la bandeja y el hecho de negocio permanecen disponibles.

**Riesgos**

- Los workers pueden competir por CPU, memoria o conexiones con el monolito.
- Un contrato ambiguo puede trasladar reglas de negocio a Comunicación.
- La migración de registros puede producir duplicados o cambiar estados históricos.

**Criterio de finalización**

- Todos los productores usan el contrato de M-06 y no acceden a su persistencia.
- La deduplicación pasa casos de reintento y timeout sin efectos repetidos.
- Una campaña representativa mantiene el p95 interactivo dentro del objetivo acordado.
- Se restaura un conjunto de estados y eventos dentro del RTO/RPO aprobado.
- No quedan consumidores desconocidos de la representación antigua.

**Ruta de retorno**

Mantener el adaptador interno y la ruta anterior hasta completar la reconciliación. Si falla la migración, detener nuevas escrituras en el destino, conservar el origen como fuente temporal, reconciliar por identificador y volver a dirigir solicitudes a la ruta anterior. No se deben fusionar dos estados mediante actualización manual no auditada.

### Etapa 2. Introducir el Strangler para el procesamiento de entregas

**Objetivo**

Separar gradualmente el procesamiento de notificaciones de la ruta inicial sin cambiar el contrato de M-06 ni la experiencia de los consumidores. La fachada decide si una solicitud se procesa por el camino legado o por el worker nuevo.

**Cambio de ownership**

- M-06 conserva el ownership de estado, bandeja y política semántica.
- El worker nuevo obtiene ownership operativo del procesamiento asignado a su cohorte: lectura de trabajo, llamada al correo y actualización idempotente del estado.
- El camino legado queda como consumidor de compatibilidad, no como fuente alternativa de reglas.

**Compatibilidad necesaria**

- La fachada debe aceptar solicitudes de consumidores antiguos y nuevos.
- El formato de mensajes debe versionarse de manera compatible; consumidores nuevos deben tolerar estados antiguos y consumidores antiguos no deben recibir significados nuevos sin traducción.
- La doble ejecución no está permitida. El enrutamiento debe asignar una cohorte o clave de notificación a un solo camino.
- Las respuestas y estados deben conservar la semántica observable para la bandeja y los productores.

**Datos migrados o duplicados**

- No duplicar escrituras de entrega. El nuevo worker lee trabajos desde una vista, cola o réplica derivada controlada.
- Copiar inicialmente una muestra de eventos para comparación sin enviarlos; contrastar decisión de destinatario, plantilla, estado y error.
- Luego mover cohortes por empleado, campaña o tipo de notificación, conservando un único registro de estado en M-06.
- Los trabajos pendientes se migran con su clave idempotente, fecha de creación, número de intentos y vencimiento.

**Mecanismo de observación**

- Comparar, por cohorte, p95 de aceptación, edad de cola, tasa de entrega, rebotes, reintentos, duplicados y errores por versión.
- Usar trazas o correlación de evento desde productor hasta bandeja y correo.
- Mantener una comparación en modo sombra para mensajes no sensibles o sin envío real cuando sea posible.
- Vigilar que la ruta nueva no aumente el error o la latencia del servicio principal.

**Riesgos**

- La fachada puede acumular reglas y convertirse en un monolito nuevo difícil de retirar.
- La duplicación temporal de datos puede producir divergencia o problemas de privacidad.
- Un error de enrutamiento puede enviar dos veces o saltarse una notificación.
- El camino legado puede permanecer indefinidamente si no tiene una fecha o métrica de retiro.

**Criterio de finalización**

- El worker nuevo procesa el 100% de una cohorte acordada durante un periodo suficiente para incluir campañas y fallos de correo.
- No hay duplicados lógicos y la reconciliación de estados es completa.
- Las métricas de la ruta nueva cumplen o superan las de la ruta anterior.
- La recuperación de trabajos pendientes funciona y no requiere escribir directamente en la fuente antigua.
- Se identifica el último consumidor legado y existe una fecha de retiro aprobada.

**Ruta de retorno**

Reducir el porcentaje de tráfico del worker nuevo a cero mediante la fachada y dirigir nuevas solicitudes al camino legado. Dejar los trabajos nuevos en `Pendiente` o procesarlos una sola vez según su clave; no reenviar automáticamente trabajos cuyo resultado sea incierto. Reconciliar los estados del worker antes de reabrir el tráfico.

### Etapa 3. Extraer M-06 como servicio solo si existe evidencia

**Objetivo**

Convertir Comunicación y bandeja en un proceso operable por separado únicamente si la etapa 2 demuestra un beneficio concreto de aislamiento, escalado, despliegue o reutilización corporativa.

**Cambio de ownership**

- El servicio de Comunicación pasa a ser la única fuente maestra de notificaciones y bandeja.
- El monolito conserva la propiedad de asignaciones, aprendizaje y certificación, y publica hechos mediante un contrato versionado.
- El servicio no lee ni escribe tablas del monolito. La autorización se resuelve mediante un contrato o contexto autorizado, sin crear una segunda fuente de identidad.
- El equipo responsable del servicio acepta operación, seguridad, backups, restauración, capacidad y soporte.

**Compatibilidad necesaria**

- Mantener el contrato de solicitud y eventos de M-06 durante la transición.
- Admitir productores antiguos mediante la fachada y consumidores antiguos mediante una vista o API compatible hasta su retiro.
- Definir timeouts, reintentos, duplicados, orden no garantizado, versionado, errores y estados pendientes.
- Separar la confirmación de aceptación durable del resultado final de entrega.

**Datos migrados o duplicados**

- Cargar una copia inicial de notificaciones, bandeja y trabajos pendientes con sus identificadores y fechas.
- Capturar cambios posteriores hasta que el servicio demuestre paridad; durante la transición el servicio nuevo puede recibir una réplica de eventos, pero solo un componente actualiza el ownership.
- Comparar conteos, estados, destinatarios autorizados, fechas, reintentos y auditoría antes de cambiar la fuente maestra.
- Exportar y probar restauración antes de retirar el almacenamiento anterior.

**Mecanismo de observación**

- Métricas separadas de servicio y dependencia: p95 de aceptación, latencia de correo, profundidad y edad de cola, disponibilidad, errores, duplicados, costo de operación y tiempo de recuperación.
- Trazabilidad distribuida con correlación desde el hecho de negocio hasta la entrega.
- Pruebas de contrato en productores y consumidores antiguos y nuevos.
- Prueba de partición: el monolito debe poder registrar el hecho de negocio y dejar la notificación pendiente sin inventar una entrega.

**Riesgos**

- Aparecen fallos parciales, latencia de red, consistencia eventual y una segunda operación que el equipo debe sostener.
- El servicio puede convertirse en dependencia crítica si no existe una degradación clara.
- Migrar la bandeja y trabajos pendientes puede exponer datos o duplicar entregas.
- La extracción puede costar más que el beneficio si solo reduce latencia interna marginal.

**Criterio de finalización**

- Existe un equipo operativo responsable con capacidad de guardia, monitoreo y recuperación.
- El contrato pasa pruebas de compatibilidad para todos los consumidores.
- El servicio nuevo cumple los SLO, RTO/RPO y requisitos de autorización durante una ventana con carga normal, pico y fallo de correo.
- La fuente maestra de M-06 está definida y no existen escrituras activas en el almacenamiento antiguo.
- La ruta de retorno y exportación se han probado, no solo documentado.
- El costo adicional y el beneficio medido fueron aceptados por producto, TI y operación.

**Ruta de retorno**

Usar la fachada para devolver el tráfico al M-06 que todavía pueda operar dentro del monolito. Si el servicio ya posee la fuente maestra, restaurar una exportación validada o ejecutar una migración inversa por lotes con deduplicación y corte controlado. No volver a dos fuentes maestras activas: la reversión debe incluir un único propietario por cada intervalo de tiempo.

## 5. Matriz de decisión de evolución

| Camino | Cuándo conviene | Qué resuelve | Qué añade | Decisión para SkillHub |
|---|---|---|---|---|
| **Monolito a monolito modular** | Cuando el problema principal es acoplamiento, ownership o pruebas, no escala independiente. | Protege límites, contratos y datos sin introducir red ni fallos parciales. | Disciplina de dependencias y posible trabajo de migración interna. | Primer paso obligatorio antes de separar M-06. |
| **Strangler pattern** | Cuando existe un flujo delimitado, un contrato estable y una ruta legado que puede enrutar por cohortes. | Permite mover procesamiento gradualmente y conservar retorno. | Fachada, compatibilidad, doble operación temporal y observabilidad. | Segundo paso, condicionado a métricas y datos reconciliables. |
| **Extracción a servicio** | Cuando hay escalado distinto, aislamiento de fallos, despliegue independiente, tecnología incompatible o consumidor corporativo compartido. | Aísla proceso y ownership operativo de M-06. | Red, latencia, fallos parciales, consistencia eventual, contratos versionados y operación adicional. | Solo tercer paso y solo con evidencia de necesidad. |

No se recomienda una reescritura total: perdería datos, comportamiento, conocimiento operativo y rutas de recuperación ya definidas. Tampoco se recomienda una extracción directa desde el estado actual, porque todavía no se ha demostrado que los contratos y la recuperación sean operables fuera del monolito.

## 6. Architecture fitness functions

### FF-01. Aislamiento de ownership y dependencias

**Propiedad:** ningún módulo productor escribe la persistencia de M-06 ni consulta directamente el adaptador de correo. Las solicitudes deben pasar por el contrato de Comunicación y bandeja.

**Medición repetible:** en cada build y revisión de arquitectura, analizar dependencias de módulos y buscar accesos fuera de las interfaces permitidas. La prueba debe fallar si M-02, M-03, M-04 o cualquier consumidor importa persistencia interna de M-06 o un cliente concreto de correo.

**Respuesta ante incumplimiento:** bloquear la promoción de la etapa, eliminar el acceso directo o crear un contrato explícito antes de continuar. Si ya está desplegado, registrar una deuda con propietario y detener la extracción hasta recuperar ownership único.

### FF-02. Entrega desacoplada sin regresión del servicio principal

**Propiedad:** las notificaciones pueden quedar pendientes sin degradar las operaciones interactivas ni duplicar efectos.

**Medición repetible:** ejecutar una campaña representativa y un escenario de correo indisponible. Verificar p95 de aceptación de notificación dentro del objetivo provisional de 2 s, p95 de operaciones interactivas dentro del SLO acordado, cero duplicados lógicos y 100% de mensajes con estado explícito. Medir edad máxima de cola y tasa de reconstrucción exitosa.

**Respuesta ante incumplimiento:** detener el avance de tráfico hacia la etapa siguiente, reducir o pausar workers, volver a la ruta anterior mediante la fachada y corregir capacidad, backpressure, idempotencia o recuperación. No compensar un fallo de entrega haciendo síncrona la operación de negocio.

Las funciones son señales de decisión, no sustituyen pruebas de seguridad, recuperación ni revisión humana. Deben tener un propietario en operación y revisarse si cambian los SLO o el volumen del producto.

## 7. Deuda técnica temporal

### Deuda: fachada y adaptador de compatibilidad durante la transición

Durante las etapas 1 y 2 puede existir una fachada que traduzca solicitudes antiguas al contrato de M-06 y dirija tráfico al camino legado o nuevo. Esta duplicación es deliberada, pero aumenta estados, métricas y superficie de diagnóstico.

- **Propietario:** equipo de SkillHub con operación.
- **Interés:** cada versión adicional puede ocultar un error de contrato y prolongar el soporte de consumidores antiguos.
- **Condición explícita de retiro:** retirar la fachada de compatibilidad cuando todos los consumidores estén en el contrato versionado, la última cohorte haya completado el periodo de observación, no existan trabajos pendientes en el camino legado y se haya probado la ruta de retorno.
- **Fecha de revisión:** al finalizar la primera campaña representativa de la etapa 2 y, como máximo, en la revisión de arquitectura posterior a la etapa 2.
- **Acción de pago:** bloquear nuevos consumidores antiguos, migrar los existentes, eliminar el enrutamiento legado, archivar sus métricas y actualizar el ADR-011.

La duplicación de datos de migración también es temporal. Su condición de retiro es una reconciliación completa, una copia recuperable del destino y la confirmación del nuevo propietario. No debe convertirse en dos fuentes de escritura permanentes.

## 8. Riesgos transversales y controles

| Riesgo | Control durante la ruta | Criterio de parada |
|---|---|---|
| Divergencia de datos entre origen y destino | Identificadores estables, carga inicial, captura de cambios, reconciliación por conteo y contenido, ownership único de escritura. | Diferencias no explicadas en destinatario, estado, fecha o auditoría. |
| Duplicación de entregas | Clave idempotente por evento-destinatario-canal-tipo, deduplicación y no doble enrutamiento. | Cualquier duplicado lógico sin explicación durante prueba o campaña. |
| Dependencia de red o correo | Timeouts, circuit breaker, cola durable y estado pendiente; probar partición y caída del correo. | La aplicación bloquea una operación crítica o marca entrega no confirmada como exitosa. |
| Falta de operación | Runbooks, responsables, métricas, backups y ejercicios de restauración antes de mover ownership. | No existe responsable o la restauración no cumple el objetivo acordado. |
| Extracción sin beneficio | Comparar costo, p95, capacidad, errores y ritmo de cambio contra la etapa anterior. | El beneficio no compensa la complejidad o no puede demostrarse con métricas. |
| Privacidad durante copias | Minimizar datos, cifrar copias según política, limitar acceso y auditar exportaciones. | Copia o consumidor nuevo expone datos fuera del alcance autorizado. |

## 9. Supuestos y preguntas pendientes

1. **Crecimiento:** ¿La población, frecuencia de campañas, canales y volumen de notificaciones crecerán más allá de lo que puede sostener un worker dentro del monolito durante los próximos 12 a 24 meses?
2. **Contrato de correo:** ¿TI corporativo garantiza límites, autenticación, disponibilidad, rebotes y soporte suficientes para una cola interna y, posteriormente, para un servicio separado?
3. **Contrato de eventos:** ¿Asignaciones, aprendizaje y certificación pueden publicar hechos con identificadores estables y versión sin cambiar las reglas de negocio ni romper consumidores existentes?
4. **Prioridades de producto:** ¿La prioridad futura es soportar campañas más grandes, agregar canales, reducir tiempo de despliegue de Comunicación o compartir la capacidad con otras aplicaciones?
5. **Operación:** ¿Existe un equipo que pueda operar un servicio separado con guardias, seguridad, capacidad, backup y recuperación, o el monolito modular seguirá siendo la opción más responsable?
6. **Datos y retención:** ¿Qué periodo deben conservarse la bandeja, los estados de entrega, los mensajes no procesables y la auditoría, y qué datos pueden duplicarse temporalmente durante una migración?

## 10. Criterio global de avance

La ruta solo avanza cuando se cumplen simultáneamente:

- contrato compatible y ownership único documentado;
- datos migrados o duplicados reconciliados;
- fitness functions dentro de objetivo;
- observabilidad suficiente para distinguir fallo de aplicación, cola, correo y red;
- recuperación y ruta de retorno probadas;
- responsable operativo y costo aceptados;
- ausencia de regresión en autorización, integridad, p95 y experiencia de usuario.

Si una etapa falla, el estado anterior es válido y se vuelve a él mediante su ruta de retorno. La evolución no se considera incompleta por no llegar a servicio separado: el resultado correcto puede ser mantener el monolito modular si las métricas demuestran que satisface los objetivos.

## 11. Estado esperado al cierre de la ruta

El estado objetivo no es necesariamente un microservicio. Es un M-06 con ownership claro, contrato versionado, datos recuperables, observabilidad y un mecanismo de despliegue que puede permanecer dentro del monolito o cruzar a un servicio si la evidencia lo exige. La arquitectura gana una opción de evolución sin pagar por adelantado toda su complejidad.