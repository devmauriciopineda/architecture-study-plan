# Semana 2. Registro de requisitos y restricciones de SkillHub

## 1. Propósito y trazabilidad

Este registro convierte la visión, los requisitos de producto y el mapa sistémico de SkillHub en condiciones observables para evaluar decisiones arquitectónicas posteriores. No prescribe módulos, servicios, tablas ni infraestructura.

Las referencias utilizadas son:

- **PV:** `product/product-vision.md`.
- **PR:** `product/product-requirements.md`, incluidos sus casos de uso (CU), requisitos funcionales (RF), reglas de negocio (RN) y requisitos no funcionales (RNF).
- **SM:** `course/semana-01-system-map.md`.

Los elementos derivados directamente de PV, PR o SM tienen esa referencia. Los valores marcados como **hipótesis** son estimaciones de trabajo y deben validarse antes de usarlos para dimensionar una solución.

## 2. Requisitos funcionales

| ID | Prioridad | Actor | Trazabilidad | Comportamiento esperado | Criterio de aceptación |
|---|---|---|---|---|---|
| RF-01 | Must | Instructor | PR CU-01, RF-01 a RF-05 | Puede crear y editar un curso en `Borrador` con título, descripción, módulos, contenidos y evaluación, y enviarlo a revisión cuando cumpla los mínimos definidos. | Dado un instructor autorizado, al guardar un curso incompleto permanece en `Borrador`; al completar los mínimos y enviarlo, queda en `En revisión` y se registra el envío. |
| RF-02 | Must | Administrador de RR. HH. | PR CU-02, RF-06 a RF-09 | Puede aprobar un curso o devolverlo a borrador con un motivo; solo un curso aprobado puede publicarse. | Al aprobar, el curso queda `Publicado` y aparece como disponible según su configuración; al devolverlo, vuelve a `Borrador`, conserva el motivo y el instructor puede consultarlo. |
| RF-03 | Must | Administrador de RR. HH. | PR CU-03, RF-11 a RF-16 | Puede hacer disponible un curso en catálogo o asignarlo a empleados concretos, áreas o roles, con fecha límite cuando corresponda. | Una asignación válida crea un registro visible para cada empleado objetivo con curso, fecha y estado; una asignación activa duplicada no crea una segunda obligación. |
| RF-04 | Must | Empleado | PR CU-04, RF-17 a RF-19, RN-06 | Puede consultar sus cursos, revisar contenidos y registrar progreso; el sistema impide completar si falta contenido obligatorio. | Si falta un contenido obligatorio, una evaluación aprobada no cambia el curso a `Completado`; después de revisar todos los contenidos, el progreso refleja el cumplimiento. |
| RF-05 | Must | Empleado | PR CU-04, RF-20 a RF-23, RN-07 | Puede presentar una evaluación; el sistema registra cada intento, calcula el resultado, aplica la calificación mínima y limita los intentos según la política. | Cada intento conserva fecha, respuestas, calificación y resultado; al alcanzar el límite, un nuevo intento es rechazado salvo desbloqueo autorizado. |
| RF-06 | Must | Empleado / RR. HH. | PR CU-05, RF-24 y RF-25, RN-08 | El empleado puede solicitar desbloqueo con motivo y RR. HH. puede aprobarlo o rechazarlo dejando decisión registrada. | Una aprobación habilita la cantidad de intentos definida por la política; un rechazo mantiene el bloqueo y deja visible el estado cerrado de la solicitud. |
| RF-07 | Must | SkillHub / Empleado | PR CU-04, RF-26 a RF-29, RN-09 | Al cumplirse contenidos y evaluación, el sistema marca el curso como completado, genera un certificado asociado y permite consultarlo y descargarlo. | Para un curso completado existe un certificado vinculado al empleado y curso; no existe certificado para un curso que no cumpla las condiciones de finalización. |
| RF-08 | Should | SkillHub / Empleado | PR RF-30 y RF-31 | Genera notificaciones internas y por correo para asignaciones, resultados relevantes, certificados y decisiones de revisión, registrando el estado de entrega. | Cada evento requerido crea una notificación interna; el fallo del correo queda registrado y no revierte la asignación, el resultado ni el certificado. |
| RF-09 | Must | Administrador de RR. HH. | PR CU-06, RF-39 a RF-42 | Permite consultar cobertura, progreso, finalización y resultados filtrados por curso, área, rol y periodo, respetando permisos. | Con un conjunto de datos conocido, cada filtro devuelve únicamente la población correspondiente; un empleado solo obtiene sus propios resultados y RR. HH. autorizado puede consultar el alcance organizacional permitido. |
| RF-10 | Must | Sistema de empleados / SkillHub | PR RF-35 a RF-38, RN-11; SM | Consulta nombre, área, rol y estado activo al sistema corporativo; usa esos datos como fuente maestra y bloquea operaciones de usuarios inactivos. | Un cambio de rol o área externo se refleja en una consulta posterior; un usuario marcado inactivo no puede iniciar operaciones protegidas; una indisponibilidad externa se comunica sin corromper datos locales. |

## 3. Requisitos no funcionales y atributos de calidad

En cada escenario, **estímulo** describe lo que ocurre, **contexto** fija las condiciones y **medida** define cómo verificarlo. Las métricas pendientes no deben convertirse en objetivos implícitos.

| ID | Atributo | Prioridad | Trazabilidad | Escenario, medida y condición verificable |
|---|---|---|---|---|
| RNF-01 | Rendimiento y latencia | Must | PR RNF-10 pendiente; SM flujos 4, 7 y 9 | **Escenario:** usuario consulta catálogo, progreso o reporte bajo carga normal. **Medida:** percentil 95 de tiempo desde solicitud hasta respuesta. **Umbral:** pendiente de validar por operación; como hipótesis inicial, catálogo/progreso <= 2 s y reportes básicos <= 5 s con hasta 50 usuarios concurrentes. **Verificación:** prueba de carga con dataset representativo. |
| RNF-02 | Disponibilidad y recuperación | Must | PR RNF-11 a RNF-15; SM dependencia de infraestructura | **Escenario:** durante horario laboral acordado, un usuario intenta acceder a una función del MVP. **Medida:** porcentaje mensual de tiempo disponible y tiempo de recuperación tras fallo. **Umbral:** horario laboral y RTO/RPO formales pendientes; condición mínima conocida: respaldos diarios y procedimiento de recuperación manual probado antes de producción. |
| RNF-03 | Seguridad, privacidad y autorización | Must | PR RNF-01 a RNF-06, RN-10 | **Escenario:** empleado, instructor o RR. HH. intenta leer o modificar datos fuera de su permiso. **Medida:** proporción de pruebas de autorización que rechazan accesos no permitidos y completitud de bitácora. **Umbral:** 100% de los casos definidos debe rechazar el acceso indebido; contraseñas nunca en texto plano; asignaciones y certificados deben registrar actor, fecha y resultado. |
| RNF-04 | Consistencia e integridad | Must | PR RN-01 a RN-11, RF-16, RF-31 y RF-36; SM flujo 3 | **Escenario:** ocurren simultáneamente una asignación, una finalización, una notificación o una consulta de datos maestros. **Medida:** ausencia de asignaciones activas duplicadas, certificados prematuros y modificaciones locales de datos maestros. **Umbral:** 0 duplicados activos y 0 certificados sin cumplimiento; tras confirmarse un cambio de estado, las lecturas posteriores deben reflejarlo dentro de una ventana pendiente de validar. |
| RNF-05 | Mantenibilidad y evolución | Should | PV principios 3 y 5; PR decisiones pendientes | **Escenario:** un instructor cambia contenido permitido o RR. HH. cambia una regla configurable sin alterar otras capacidades. **Medida:** porcentaje de cambios del alcance previsto que puede validarse mediante pruebas y revisión sin modificar capacidades no relacionadas; tiempo de ciclo por cambio. **Umbral:** pendiente de validar; condición actual: el contenido debe poder mantenerse por instructores sin cambios de software y el MVP debe admitir evolución futura. |
| RNF-06 | Capacidad y escalabilidad | Must | PV contexto; PR RNF-07 a RNF-10 | **Escenario:** población inicial, catálogo y campaña periódica crecen dentro del horizonte del MVP. **Medida:** usuarios, cursos, documentos, operaciones y concurrencia soportados sin incumplir RNF-01. **Umbral conocido:** aproximadamente 1.000 empleados y hasta 100 cursos; concurrencia, tamaño máximo de adjuntos y crecimiento anual pendientes. |
| RNF-07 | Costo operativo | Must | PV principio de evolución; PR RNF-16 y contexto on-premise | **Escenario:** operación mensual del MVP con infraestructura y soporte disponibles en la empresa. **Medida:** costo mensual de infraestructura, almacenamiento, correo, respaldos y soporte, separado del costo inicial. **Umbral:** presupuesto máximo y capacidad de equipo pendientes de validar; condición conocida: debe poder operar on-premise sin introducir servicios externos no aprobados. |
| RNF-08 | Observabilidad y auditabilidad | Must | PR RF-31, RF-43; SM dependencias | **Escenario:** ocurre una asignación, certificado, decisión, error de integración o fallo de notificación. **Medida:** eventos con actor o proceso, fecha, entidad afectada, resultado y error cuando corresponda; tiempo para reconstruir el caso. **Umbral:** 100% de asignaciones y certificados auditables; retención y tiempo objetivo de diagnóstico pendientes de validar. |

## 4. Restricciones

| ID | Restricción | Origen y trazabilidad | Fuerza / estado | Impacto arquitectónico |
|---|---|---|---|---|
| C-01 | El sistema debe desplegarse inicialmente on-premise. | PV contexto; PR RNF-16 | Dura, actual | Limita las opciones de operación, almacenamiento, respaldo y soporte a capacidades aprobadas por la empresa. |
| C-02 | El sistema corporativo de empleados es la fuente maestra de nombre, área, rol y estado activo. | PV principio 4; PR RF-35 a RF-38 | Dura, actual | Impide editar esos datos localmente y exige tolerar cambios o indisponibilidad del sistema externo. |
| C-03 | El MVP se limita a aproximadamente 1.000 empleados, hasta 100 cursos y el ciclo básico de capacitación. | PV alcance; PR fuera del alcance y RNF-07 a RNF-09 | Dura, actual | Favorece una solución proporcionada; no justifica anticipar comercio electrónico, aplicación móvil o analítica predictiva. |
| C-04 | Los resultados individuales deben estar protegidos por rol; instructores y otros empleados no pueden consultarlos. | PR RN-10, RF-41 y RF-42; SM actores | Dura, actual | La autorización y la privacidad son condiciones de aceptación, no preferencias de interfaz. |
| C-05 | El correo corporativo es el canal inicial para recuperación y notificaciones, pero el envío no puede ser condición para completar. | PR RF-30, RF-31 y dependencias; SM frontera | Blanda, actual para MVP | Requiere separar el resultado local de la entrega externa y permite cambiar de proveedor o canal sin perder evidencia. |

## 5. Supuestos

| ID | Supuesto / hipótesis | Evidencia disponible | Forma y momento de validación | Consecuencia si es falso |
|---|---|---|---|---|
| A-01 | El sistema de empleados ofrece una API síncrona con nombre, área, rol y estado activo por empleado. | PR dependencias, RF-35 y contexto de integración prioritaria. | Revisar contrato con TI corporativo y ejecutar una prueba de integración antes de cerrar el diseño de integración. | Habría que redefinir sincronización, permisos y asignaciones; los requisitos RF-03 y RF-10 quedarían bloqueados. |
| A-02 | Los empleados disponen de correo corporativo utilizable para recuperación y notificaciones. | PV usuarios; PR RF-30, RF-34 y dependencias. | Confirmar cobertura de cuentas y una entrega de prueba con RR. HH. y mensajería. | Debe definirse otro mecanismo de recuperación o un canal alternativo; aumenta riesgo operativo y de adopción. |
| A-03 | RR. HH. dispone de una política vigente de calificación mínima, máximo de intentos y desbloqueo. | PR RN-07, decisiones pendientes y CU-05. | Obtener la política aprobada y validar ejemplos de cálculo con RR. HH. antes de implementar evaluación. | No se puede determinar finalización ni desbloqueo de forma consistente; RF-05 a RF-07 deben permanecer configurables o pendientes. |
| A-04 | La infraestructura on-premise puede alojar aplicación, documentos, respaldos y operación básica para la población inicial. | PV contexto; PR RNF-16 y dependencias. | Inventariar capacidad disponible y ejecutar una prueba de capacidad con 1.000 empleados, 100 cursos y adjuntos representativos. | Se debe renegociar alcance o despliegue; el costo, la disponibilidad y la recuperación cambiarían sustancialmente. |
| A-05 | La primera versión no necesita versionado avanzado de cursos publicados ni múltiples proveedores de identidad. | PV fuera del MVP; PR fuera del alcance y dependencias. | Validar con RR. HH., instructores y seguridad mediante revisión de escenarios de corrección y acceso. | Aparecerían requisitos de migración, compatibilidad, identidad y trazabilidad que pueden alterar el alcance del MVP. |

## 6. Caracterización inicial de la carga

| Dimensión | Valor conocido | Estimación o hipótesis | Dato faltante / riesgo |
|---|---|---|---|
| Población | Aproximadamente 1.000 empleados; roles de empleado, instructor y RR. HH. | La mayoría consultará su plan ocasionalmente y una fracción será instructora o administradora. | Cantidad real por rol, áreas y crecimiento previsto. |
| Cursos y contenidos | Hasta 100 cursos en el volumen inicial; cada curso puede incluir varios documentos, enlaces, videos externos y evaluación. | Promedio inicial de 5 módulos por curso y 3 documentos por curso: 500 módulos y 300 documentos. **Hipótesis.** | Tamaño máximo y promedio de documentos, distribución de preguntas y peso real de los contenidos. |
| Asignaciones | Pueden dirigirse a empleados, áreas o roles; no deben duplicarse activamente. | Una campaña podría afectar a 1.000 empleados y generar cerca de 1.000 estados de asignación. **Hipótesis.** | Número de campañas por mes, reasignaciones, cancelaciones y fechas de vencimiento. |
| Operaciones interactivas | Consultas de catálogo, plan individual, contenido, progreso, evaluación, certificados y reportes. | Aproximadamente 10 consultas interactivas por empleado por semana: 10.000 por semana. **Hipótesis.** | Distribución lectura/escritura, duración de sesiones, tamaño de respuestas y tasa de evaluación. |
| Operaciones administrativas | Creación y revisión de cursos, asignaciones, desbloqueos y consultas de RR. HH. | Decenas de instructores y un grupo pequeño de administradores. **Hipótesis.** | Cantidad exacta de instructores, revisores, decisiones y solicitudes por periodo. |
| Notificaciones | Asignaciones, resultados, certificados y decisiones generan mensajes internos y potencialmente correo. | Una campaña de 1.000 empleados puede producir una ráfaga cercana a 1.000 notificaciones. **Hipótesis.** | Límite de entrega del correo, tasa de rebote, reintentos y volumen de eventos secundarios. |
| Concurrencia | No está definida en PR; puede concentrarse durante campañas. | Hasta 50 usuarios simultáneos para el primer escenario de rendimiento. **Hipótesis.** | Máximo simultáneo, picos por hora, comportamiento de una campaña y carga de reportes. |
| Retención | Deben conservarse progreso, resultados, certificados y bitácora según políticas aplicables. | La retención será de varios años por tratarse de evidencia de capacitación. **Hipótesis.** | Periodo legal o corporativo, volumen anual, política de eliminación y necesidades de auditoría histórica. |

### Lectura de la carga

La carga inicial parece moderada en población y catálogo, pero puede presentar ráfagas en campañas de asignación, notificaciones y consultas de RR. HH. El riesgo principal no es solo el volumen total: son la concurrencia desconocida, los documentos, la dependencia del correo y los reportes que combinan datos por periodo. Los valores hipotéticos deben confirmarse antes de convertirlos en objetivos de capacidad.

## 7. Tensiones entre atributos

1. **Costo frente a disponibilidad:** operar on-premise con respaldos diarios y recuperación manual contiene inversión y complejidad inicial, pero ofrece menor disponibilidad y recuperación más lenta que una operación redundante. La decisión debe partir del horario laboral, impacto de interrupciones y presupuesto validado.
2. **Consistencia frente a latencia:** validar siempre el estado del empleado y aplicar inmediatamente cambios de rol mejora autorización e integridad, pero puede añadir latencia o hacer depender cada operación de la API externa. Permitir datos locales temporalmente mejora respuesta, pero exige definir cuándo quedan obsoletos y qué operaciones se bloquean.
3. **Seguridad frente a mantenibilidad:** controles de autorización y auditoría detallados protegen resultados, pero aumentan el número de escenarios que deben probarse y conservarse. Reducir controles para simplificar el MVP no es aceptable si expone datos individuales; debe buscarse una política clara y repetible.

## 8. Decisiones y datos pendientes priorizados

| Prioridad | Decisión o dato a validar | Partes interesadas | Por qué bloquea decisiones posteriores |
|---|---|---|---|
| 1 | Contrato, disponibilidad, latencia y semántica de datos de la API del sistema de empleados. | TI corporativo, RR. HH., seguridad | Define integración, consistencia, autorización y comportamiento ante indisponibilidad. |
| 2 | Objetivos de disponibilidad, RPO, RTO y horario laboral crítico. | Responsable operativo, TI, dirección | Permite comparar costo de recuperación, respaldo y continuidad. |
| 3 | Carga real: concurrencia máxima, campañas por periodo, usuarios por rol y volumen de notificaciones. | RR. HH., instructores, operación | Convierte las hipótesis de capacidad y rendimiento en escenarios de prueba. |
| 4 | Política de evaluación, desbloqueos, certificados, retención y correcciones de cursos publicados. | RR. HH., instructores, legal/compliance | Define transiciones, consistencia de resultados, auditoría y ciclo de vida de evidencias. |
| 5 | Presupuesto operativo, capacidad on-premise, límites de adjuntos y costo aceptable de soporte. | Dirección, finanzas, TI, operación | Determina qué nivel de disponibilidad, rendimiento y evolución es viable sin sobrediseñar el MVP. |

## 9. Estado de validación

El registro es suficientemente concreto para comparar alternativas en la semana 3, pero no debe tratar las hipótesis de carga, los umbrales pendientes ni las políticas no confirmadas como decisiones arquitectónicas. La siguiente revisión debe actualizar este documento con evidencia de las cinco decisiones priorizadas.