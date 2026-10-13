# ADR-011. Gestionar notificaciones dentro del monolito modular

- **Estado:** Propuesto para revisión
- **Fecha:** 2026-10-12
- **Decisión:** Mantener la lógica y el estado de notificaciones en el módulo M-06 Comunicación y bandeja, usando una cola durable interna y workers separados de la ruta interactiva.
- **Alcance:** Notificaciones de asignaciones, resultados relevantes, certificados y decisiones de revisión, mediante bandeja interna y correo corporativo.
- **Fuera de alcance:** Elegir proveedor, implementar la cola, definir una topología concreta, crear una plataforma corporativa de mensajería o resolver todos los canales futuros.

## 1. Contexto y problema

SkillHub debe comunicar asignaciones, resultados, certificados y decisiones de revisión a aproximadamente 1.000 empleados. Cada hecho de negocio debe conservarse aunque el correo corporativo esté lento o indisponible. La entrega de correo no puede ser condición para activar una asignación, registrar un resultado o emitir un certificado.

La solución necesita aceptar ráfagas de notificaciones, registrar estados de entrega, evitar duplicados, reintentar fallos transitorios y permitir recuperación o reprocesamiento controlado. La capacidad inicial debe funcionar on-premise y no debe introducir una plataforma distribuida cuya necesidad aún no esté demostrada.

La decisión pendiente es **dónde ubicar y cómo operar la capacidad de notificaciones del MVP**:

1. dentro de SkillHub con una cola y workers mínimos;
2. mediante una capacidad gestionada adquirida;
3. mediante una plataforma de mensajería autogestionada separada.

La decisión no elige tecnología, proveedor ni producto. Define el límite de responsabilidad, el contrato y el nivel de operación que se acepta para el MVP.

## 2. Drivers de decisión

Ordenados por prioridad:

1. **Integridad y autorización:** ningún aviso debe producir efectos duplicados ni revelar resultados o certificados a un destinatario incorrecto.
2. **Aislamiento de fallos:** una caída del correo no debe bloquear asignaciones, evaluaciones ni certificaciones.
3. **Operabilidad:** deben poder diagnosticarse profundidad y edad de cola, reintentos, entregas y mensajes no procesables.
4. **Costo proporcional:** la solución debe caber en el presupuesto y las capacidades on-premise del MVP, incluyendo soporte, recuperación y salida.
5. **Rendimiento bajo ráfaga:** una campaña cercana a 1.000 empleados no debe bloquear operaciones interactivas; la aceptación del trabajo debe ser rápida y durable.
6. **Evolución y portabilidad:** debe ser posible cambiar el mecanismo de transporte o agregar un canal sin cambiar las reglas de asignación, aprendizaje o certificación.

## 3. Restricciones

Estas condiciones limitan las alternativas y no son preferencias técnicas:

| Restricción | Fuente | Impacto |
|---|---|---|
| El MVP debe poder desplegarse inicialmente on-premise. | Requisitos de producto y semana 9 | Una alternativa que dependa de un servicio externo no está disponible hasta validar red, seguridad, continuidad y salida. |
| El correo corporativo es el canal inicial de correo. | Requisitos de producto y semana 5 | SkillHub debe aislar la entrega mediante un adaptador; no controla la disponibilidad completa del canal. |
| El sistema corporativo de empleados es la fuente maestra de identidad organizacional. | Semanas 2, 4 y 6 | Comunicación no puede crear una identidad o permiso paralelo; debe usar destinatarios autorizados y datos mínimos. |
| El monolito modular es la forma provisional de despliegue. | Semana 3 | Separar la mensajería requiere evidencia operacional, no solo una preferencia de independencia. |
| Asignaciones, resultados y certificados son datos de negocio con invariantes. | Semanas 2, 6 y 8 | Una notificación fallida no puede revertir el hecho de negocio; los reintentos deben ser idempotentes. |

## 4. Supuestos y validación

| ID | Supuesto | Confianza | Cómo validarlo | Responsable |
|---|---|---|---|---|
| S-01 | Una campaña inicial puede producir cerca de 1.000 notificaciones en una ráfaga. | Media | Ejecutar una prueba con empleados, cursos y destinatarios representativos; medir profundidad, edad y throughput. | RR. HH. y operación |
| S-02 | Existe una capacidad corporativa aprobada para entregar correo desde on-premise. | Media-baja | Confirmar contrato, límites, autenticación, tasa de rechazo, disponibilidad y procedimiento de soporte. | TI corporativo |
| S-03 | La bandeja interna puede persistirse junto con el estado de notificación y auditar destinatario, evento, canal y resultado. | Alta | Revisar el modelo de ownership de M-06 y ejecutar casos de duplicación, reintento y autorización. | Equipo de SkillHub y seguridad |
| S-04 | Los eventos de negocio o la auditoría permiten reconstruir trabajos no entregados tras perder una cola. | Media | Simular pérdida de cola y comprobar que se pueden reconstruir mensajes sin duplicar asignaciones, resultados o certificados. | Equipo de SkillHub y operación |
| S-05 | El costo y el personal disponible para operar una cola interna simple son menores que el costo total de una plataforma separada durante el MVP. | Baja-media | Comparar horas de operación, soporte, respaldo, recuperación y salida con las alternativas gestionada y autogestionada. | Dirección, finanzas y TI |

Si S-02 o S-04 resultan falsos, la decisión debe revisarse antes de producción. Si S-01 o S-05 cambian, pueden activar una revisión sin invalidar automáticamente el contrato de notificación.

## 5. Alternativas consideradas

Todas se comparan con los mismos drivers: integridad, aislamiento, operabilidad, costo total, rendimiento bajo ráfaga y evolución.

### A. Módulo M-06 con cola durable interna

SkillHub posee el estado de notificación, la bandeja, el contrato de evento, el worker y el adaptador del correo corporativo. La operación principal solo espera aceptación durable del trabajo. Los reintentos son limitados y la deduplicación usa evento, destinatario, canal y tipo.

**Beneficios:** encaja con on-premise, reduce dependencias, mantiene cerca la autorización y la auditoría, y permite entregar el MVP rápidamente. La cola desacopla el correo de la transacción de negocio sin crear una segunda plataforma.

**Costos:** el equipo debe operar estados, métricas, respaldos, restauración y capacidad. La cola comparte inicialmente parte del dominio de fallo y puede competir por recursos con la aplicación.

**Riesgos:** una implementación mínima puede convertirse en una plataforma accidental si se agregan canales, consumidores y garantías sin límites. La recuperación puede ser insuficiente si no se prueban eventos y auditoría como fuentes de reconstrucción.

### B. Capacidad gestionada adquirida

SkillHub conserva el contrato y el estado de negocio, pero delega transporte, cola, reintentos y parte de la observabilidad a un servicio gestionado aprobado.

**Beneficios:** reduce trabajo rutinario y puede aportar elasticidad, durabilidad y capacidad operativa especializada.

**Costos:** introduce tarifas, conectividad, transferencia, soporte contractual y dependencia de APIs, límites y formatos. La empresa sigue teniendo que resolver autorización, bandeja, auditoría y recuperación de sus propios datos.

**Riesgos:** puede existir vendor lock-in, una falsa sensación de durabilidad o una salida costosa. También puede no cumplir la restricción on-premise si el servicio no está aprobado o el enlace falla.

### C. Plataforma de mensajería autogestionada

La empresa opera una plataforma separada, con sus propios workers, retención, monitoreo, respaldos y recuperación.

**Beneficios:** ofrece aislamiento de recursos, múltiples consumidores, mayor control de retención y potencial para compartir la capacidad con otras aplicaciones.

**Costos:** requiere personal especializado, parches, upgrades, seguridad, capacidad permanente, guardias, documentación y ejercicios de recuperación.

**Riesgos:** añade un dominio de fallo y un producto operativo antes de que exista una necesidad probada. Una mala actualización o restauración puede afectar notificaciones de varias aplicaciones.

## 6. Decisión

Se propone adoptar **A: módulo M-06 con cola durable interna y workers separados lógicamente de la ruta interactiva**.

El contrato de M-06 será independiente del mecanismo de transporte. Los productores publicarán una solicitud o hecho con identificador único, destinatario autorizado, tipo semántico, referencia al objeto de negocio y datos mínimos. M-06 será responsable de:

- persistir el estado de la notificación;
- crear la entrada de bandeja para el destinatario autorizado;
- preparar y entregar el correo corporativo mediante un adaptador;
- registrar `Pendiente`, `Entregada`, `Fallida` o `No procesable`;
- deduplicar por evento, destinatario, canal y tipo;
- reintentar solo fallos transitorios con límite y espera creciente;
- enviar mensajes inválidos a una cola de no procesables;
- exponer métricas de profundidad, edad, entregas, reintentos y fallos;
- permitir reprocesamiento controlado sin repetir el efecto de negocio.

La asignación, evaluación o certificación no esperará la entrega final del correo. Solo requerirá una aceptación durable del trabajo o registrará la notificación como pendiente si esa aceptación no está disponible. El contenido de cada aviso debe ser mínimo y la consulta de la bandeja debe pasar por la autorización de M-05.

La cola, el worker y el adaptador son mecanismos reemplazables. Si una futura decisión adopta un servicio gestionado o una plataforma corporativa, el contrato de M-06 y los estados exportables deben conservarse.

## 7. Principios aplicables

1. **Una fuente maestra por responsabilidad:** Comunicación posee el estado de entrega; no posee la asignación, el resultado ni el certificado que originaron el aviso.
2. **La dependencia externa debe estar aislada:** el correo corporativo se invoca detrás de un adaptador y no forma parte de la transacción de negocio.
3. **La degradación debe ser explícita:** `Pendiente` o `Fallida` son estados visibles; nunca se presenta como entregado un mensaje no confirmado.
4. **La autorización es transversal:** el destinatario y quien consulta la bandeja deben validarse con el contexto organizacional autorizado.
5. **La complejidad necesita evidencia:** no se crea una plataforma distribuida hasta demostrar que la capacidad interna incumple un objetivo o que existe una necesidad compartida.
6. **Los datos deben poder recuperarse:** eventos, estados y auditoría deben conservar procedencia, identificadores y fecha suficiente para reconstruir trabajos.

## 8. Consecuencias

### Beneficios aceptados

- Una falla o lentitud del correo no bloquea asignaciones, resultados ni certificados.
- El MVP mantiene una operación compatible con on-premise y evita una plataforma adicional sin necesidad demostrada.
- El contrato de M-06 protege los límites del monolito modular y permite cambiar el mecanismo de transporte después.
- La cola y el procesamiento asíncrono absorben ráfagas mejor que una llamada síncrona desde cada operación de negocio.

### Costos aceptados

- SkillHub debe implementar y operar estados, deduplicación, reintentos, métricas, respaldos y recuperación.
- Los workers comparten inicialmente infraestructura con la aplicación, por lo que habrá que reservar capacidad y aplicar backpressure.
- La solución tendrá menor elasticidad que una plataforma gestionada y puede requerir una migración si crecen los consumidores o los canales.
- Se necesita coordinar con TI corporativo el correo, la identidad, la retención y el soporte de fallos externos.

### Riesgo residual

Si se pierde simultáneamente el estado de M-06 y la fuente de eventos o auditoría no permite reconstrucción, podrían quedar avisos pendientes sin una forma segura de reprocesarlos. El riesgo no se elimina con más reintentos; se reduce con persistencia durable, copias, identificadores idempotentes y un ejercicio de restauración. Mientras ese ejercicio no exista, la recuperación de notificaciones no está demostrada.

## 9. Riesgos y respuesta

| Riesgo | Probabilidad / impacto | Señal temprana | Mitigación | Contingencia |
|---|---|---|---|---|
| El correo corporativo rechaza o retrasa campañas. | Alta / medio | Aumento de edad de mensajes, rebotes o circuit breaker abierto. | Cola durable, límites de reintento, backpressure y monitoreo por canal. | Mantener la bandeja interna, marcar correo pendiente o fallido y reprocesar después de la recuperación. |
| Worker o cola compiten con operaciones interactivas. | Media / alto | p95 de catálogo, progreso o evaluación aumenta durante campañas. | Separar capacidad lógica, limitar consumidores y priorizar operaciones críticas. | Pausar o reducir notificaciones, conservar trabajos pendientes y restaurar la prioridad del servicio principal. |
| Duplicación por timeout o reintento. | Media / alto | Más de un registro de entrega para la misma clave o queja de mensajes repetidos. | Identificador único, deduplicación transaccional y estados idempotentes. | Detener reprocesamiento, reconciliar auditoría y corregir solo con una operación auditada. |
| No se puede reconstruir después de perder la cola. | Baja / alto | Ejercicio de restauración no alcanza RPO o produce mensajes sin origen. | Respaldar estado, eventos y auditoría; probar restauración antes de producción. | Suspender envíos inciertos, conservar hechos de negocio y ejecutar recuperación manual con revisión de RR. HH. |
| El alcance crece hasta requerir una plataforma. | Media / medio | Más canales, consumidores o volumen que el contrato inicial no soporta. | Definir límites y métricas de revisión; conservar contrato independiente del transporte. | Crear un ADR de reemplazo hacia una capacidad gestionada o corporativa. |

## 10. Deuda técnica

| Deuda | Interés o impacto | Condición de pago | Responsable |
|---|---|---|---|
| La cola interna puede no soportar varios consumidores independientes. | Aumenta acoplamiento y limita escalabilidad o prioridades. | Más de dos consumidores independientes o campañas que incumplan edad máxima durante dos periodos. | Equipo de SkillHub y arquitectura |
| La recuperación de mensajes depende inicialmente de eventos y auditoría existentes. | Una pérdida simultánea puede requerir intervención manual y provocar avisos omitidos. | El ejercicio de restauración no reconstruye dentro del RTO o no conserva el RPO acordado. | Operación y TI |
| El adaptador inicial solo cubre correo corporativo y bandeja. | Agregar canales puede forzar cambios si el contrato semántico es insuficiente. | Se aprueba un canal adicional o se requiere una plantilla con datos no contemplados. | Equipo de SkillHub |
| La capacidad de workers comparte despliegue con el monolito. | Una campaña puede aumentar consumo y dificultar escalar solo Comunicación. | Métricas muestran competencia por CPU, memoria, conexiones o latencia de la aplicación. | Operación |

Esta deuda es deliberada y visible. No se paga creando una plataforma por anticipado; se paga cuando una métrica, un evento o una prueba demuestra que el límite importa.

## 11. Reversibilidad: decisión de dos vías o de una vía

La decisión general es **de dos vías con condiciones**, porque el mecanismo de cola puede reemplazarse si M-06 conserva un contrato estable y datos exportables. Cambiar el worker, aumentar consumidores, ajustar retención o sustituir la cola son cambios relativamente reversibles.

Hay decisiones internas de **una vía o de alto costo de cambio** que deben protegerse desde ahora:

- definir el significado de cada estado de entrega;
- elegir ownership de la bandeja, eventos y auditoría;
- establecer la clave de deduplicación;
- incluir datos personales en mensajes o acoplar reglas de negocio a un proveedor;
- guardar datos en formatos no exportables;
- hacer que la entrega final sea condición de una transacción de negocio.

La reversibilidad depende de que los hechos de negocio y las notificaciones no se mezclen, de que los datos tengan formatos exportables y de que el adaptador a correo no se propague a otros módulos.

## 12. Evidencia faltante y validadores

| Evidencia | Por qué importa | Quién debe validarla | Resultado esperado |
|---|---|---|---|
| Ráfaga máxima, p95 de aceptación, profundidad y edad de cola | Confirma si el módulo mínimo soporta campañas sin degradar la aplicación. | Operación, RR. HH. y equipo de SkillHub | Prueba de carga con una campaña representativa. |
| Límite, disponibilidad, autenticación y rebotes del correo | Determina el aislamiento real y la política de reintentos. | TI corporativo y responsable de mensajería | Contrato operativo y prueba de envío controlada. |
| Restauración de estado, eventos y auditoría | Demuestra RTO/RPO y reconstrucción sin duplicados. | Operación, seguridad y equipo de SkillHub | Ejercicio documentado con resultados y datos reconciliados. |
| Presupuesto y horas de soporte | Compara el costo total con una opción gestionada o autogestionada. | Dirección, finanzas y TI | Estimación que incluya operación, recuperación y salida. |
| Datos permitidos en cada canal | Evita exposición de resultados, certificados o información organizacional. | Seguridad, privacidad y RR. HH. | Política de minimización, autorización y retención aprobada. |

## 13. Revisión, reemplazo y estado futuro

El ADR debe revisarse después de una prueba de campaña representativa y antes de producción. También debe revisarse cuando ocurra cualquiera de estos eventos:

- la edad máxima de mensajes excede el objetivo acordado en dos campañas consecutivas;
- el p95 de operaciones interactivas incumple el SLO durante el procesamiento de notificaciones;
- la restauración no cumple el RTO o RPO acordado;
- aparecen más de dos consumidores independientes o canales con garantías incompatibles;
- el costo mensual de operación propia supera el presupuesto validado durante tres meses;
- el correo corporativo cambia su contrato, límites o mecanismo de autenticación;
- otra aplicación corporativa necesita la misma plataforma y puede compartir su costo operativo.

La revisión debe usar métricas de p95 de aceptación, edad y profundidad de cola, tasa de entrega, rebotes, reintentos, mensajes no procesables, costo mensual, incidentes y resultado de restauración. Si la evidencia indica que el módulo mínimo ya no cumple, este ADR pasa a **Reemplazado** por un nuevo ADR que compare una capacidad gestionada o una plataforma compartida. No se borra este registro porque explica el contrato y los estados que seguirán existiendo durante la transición.

## 14. Decisión pendiente de aprobación

Este ADR está **Propuesto** hasta que TI corporativo confirme el canal de correo y operación confirme una prueba de ráfaga y restauración. La recomendación puede implementarse conceptualmente dentro del alcance del MVP, pero no debe presentarse como garantía de producción mientras S-02 y S-04 no estén validados.