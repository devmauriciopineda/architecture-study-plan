# Requisitos de producto: SkillHub

## 1. Propósito

SkillHub es una plataforma interna de capacitación técnica para una empresa de energía o infraestructura con aproximadamente 1.000 empleados. Este documento define la línea base funcional y no funcional del MVP, derivada de la visión de producto.

El MVP debe ser realista y suficientemente acotado para construirse como primera versión, pero debe permitir estudiar modularidad, integración, datos, comunicación síncrona y asíncrona, escalabilidad, resiliencia, seguridad y evolución arquitectónica.

## 2. Alcance del MVP

El MVP permitirá:

- Crear y editar cursos técnicos.
- Incorporar texto enriquecido, documentos, videos externos y enlaces externos.
- Enviar cursos a revisión de Recursos Humanos.
- Aprobar o devolver cursos a borrador con un motivo.
- Publicar cursos aprobados.
- Mostrar cursos publicados en un catálogo.
- Asignar cursos a empleados concretos o a una población por área o rol.
- Registrar revisión de contenidos, progreso y resultados de evaluaciones.
- Aplicar una regla corporativa de aprobación con intentos limitados.
- Permitir solicitudes de desbloqueo de intentos por parte del empleado.
- Permitir que Recursos Humanos apruebe o rechace solicitudes de desbloqueo.
- Emitir certificados automáticamente al completar un curso.
- Mostrar reportes iniciales de cobertura, finalización y resultados.
- Enviar notificaciones por correo electrónico y mostrarlas en una bandeja interna.
- Consultar el sistema de empleados mediante una API síncrona.

## 3. Fuera del alcance del MVP

- Autoría avanzada de contenidos multimedia.
- Alojamiento y procesamiento propio de videos.
- Comercio electrónico o venta de cursos.
- Analítica predictiva y recomendaciones personalizadas.
- Aplicación móvil nativa.
- Videoconferencia o aulas virtuales propias.
- Integración con múltiples sistemas de identidad.
- Administración manual de los datos maestros de empleados dentro de SkillHub.
- Versionado avanzado de cursos publicados.
- Certificados con aprobación manual previa.
- Acceso de instructores a resultados individuales de empleados.
- Rol separado de administrador técnico.

## 4. Actores y roles

### 4.1 Empleado

- Consulta el catálogo de cursos publicados.
- Consulta sus asignaciones y fechas límite.
- Se inscribe en cursos voluntarios.
- Revisa contenidos y registra su progreso.
- Presenta evaluaciones.
- Consulta sus resultados y certificados.
- Solicita desbloqueo cuando agota sus intentos.

### 4.2 Instructor

- Crea y edita cursos propios en estado borrador.
- Agrega módulos, contenidos y preguntas de evaluación.
- Define la estructura del curso.
- Envía cursos a revisión.
- Consulta la decisión de Recursos Humanos y el motivo de devolución.
- Corrige cursos devueltos y los reenvía a revisión.

El instructor no puede publicar directamente ni consultar resultados individuales de empleados.

### 4.3 Administrador de Recursos Humanos

- Revisa, aprueba o devuelve cursos.
- Publica cursos aprobados.
- Asigna cursos a empleados, áreas o roles.
- Configura o aplica fechas límite de asignaciones.
- Consulta cobertura, progreso y resultados de la organización.
- Gestiona solicitudes de desbloqueo.
- Consulta certificados.

## 5. Casos de uso principales

### CU-01: Crear y enviar curso a revisión

**Actor principal:** instructor.

1. El instructor crea un curso en estado borrador.
2. Completa título, descripción, módulos, contenidos y evaluación.
3. Guarda cambios mientras el curso está incompleto o en preparación.
4. Envía el curso a revisión.
5. SkillHub valida que el curso tenga la información mínima requerida.
6. El curso pasa a estado `En revisión`.
7. Recursos Humanos recibe una notificación.

### CU-02: Revisar y publicar curso

**Actor principal:** administrador de Recursos Humanos.

1. RR. HH. consulta los cursos pendientes de revisión.
2. Revisa la información y los contenidos del curso.
3. Aprueba o devuelve el curso.
4. Si lo devuelve, registra un motivo y el curso vuelve a `Borrador`.
5. Si lo aprueba, el curso pasa a `Publicado` y queda disponible según su configuración.
6. El instructor recibe una notificación del resultado.

### CU-03: Asignar curso o habilitar inscripción

**Actor principal:** administrador de Recursos Humanos.

1. RR. HH. selecciona un curso publicado.
2. Define si estará disponible en el catálogo, si será asignado obligatoriamente o ambas cosas.
3. Para una asignación obligatoria, selecciona empleados, áreas o roles.
4. Define una fecha límite cuando corresponda.
5. SkillHub crea las asignaciones y notifica a los empleados.

### CU-04: Completar curso y obtener certificado

**Actor principal:** empleado.

1. El empleado consulta un curso voluntario o asignado.
2. Revisa todos los módulos y contenidos obligatorios.
3. Presenta la evaluación.
4. SkillHub aplica la regla corporativa de aprobación.
5. Si aprueba y completó el contenido, el curso queda `Completado`.
6. SkillHub genera automáticamente el certificado.
7. El empleado recibe una notificación y puede descargarlo.

### CU-05: Solicitar desbloqueo de intento

**Actor principal:** empleado.

1. El empleado agota los intentos permitidos sin aprobar.
2. El curso queda no aprobado y no puede continuar la evaluación.
3. El empleado solicita un desbloqueo indicando un motivo.
4. RR. HH. revisa la solicitud.
5. Si la aprueba, SkillHub habilita nuevos intentos según la regla corporativa.
6. Si la rechaza, la solicitud queda cerrada y el curso permanece no aprobado.

### CU-06: Consultar reportes de capacitación

**Actor principal:** administrador de Recursos Humanos.

1. RR. HH. selecciona filtros de curso, área, rol o periodo.
2. SkillHub presenta cobertura, progreso, finalización y resultados.
3. RR. HH. puede consultar el detalle individual de los empleados autorizados.

## 6. Requisitos funcionales

### Cursos y workflow

- **RF-01:** El sistema debe permitir crear cursos en estado `Borrador`.
- **RF-02:** Un curso debe admitir título, descripción, módulos, contenidos y evaluación.
- **RF-03:** El sistema debe admitir texto enriquecido, documentos adjuntos, videos externos y enlaces externos.
- **RF-04:** Solo el instructor propietario debe poder editar un curso en borrador, salvo permisos de RR. HH. que se definan posteriormente.
- **RF-05:** El sistema debe validar la información mínima antes de permitir el envío a revisión.
- **RF-06:** El sistema debe manejar los estados `Borrador`, `En revisión` y `Publicado`.
- **RF-07:** RR. HH. debe poder aprobar o devolver un curso a borrador.
- **RF-08:** Una devolución debe exigir un motivo visible para el instructor.
- **RF-09:** Solo RR. HH. debe poder publicar un curso aprobado.
- **RF-10:** Un curso publicado no debe modificarse directamente sin un proceso explícito que se definirá antes de implementar cambios posteriores a la publicación.

### Catálogo, asignaciones e inscripciones

- **RF-11:** El sistema debe mostrar en el catálogo los cursos publicados disponibles.
- **RF-12:** El empleado debe poder inscribirse en cursos configurados como voluntarios.
- **RF-13:** RR. HH. debe poder asignar cursos a empleados concretos.
- **RF-14:** RR. HH. debe poder asignar cursos por área o rol utilizando datos del sistema de empleados.
- **RF-15:** Una asignación debe registrar empleado, curso, fecha de asignación, fecha límite y estado.
- **RF-16:** El sistema debe evitar asignaciones duplicadas activas para el mismo empleado y curso.
- **RF-17:** El empleado debe poder consultar sus cursos inscritos y asignados.

### Progreso, evaluaciones y finalización

- **RF-18:** El sistema debe registrar el progreso del empleado por módulo o contenido obligatorio.
- **RF-19:** El sistema debe impedir marcar un curso como completado si falta contenido obligatorio.
- **RF-20:** El sistema debe permitir configurar preguntas de evaluación dentro del curso.
- **RF-21:** El sistema debe aplicar una calificación mínima y un máximo de intentos definidos por la política corporativa.
- **RF-22:** Cada intento debe registrar fecha, respuestas, calificación y resultado.
- **RF-23:** El sistema debe impedir nuevos intentos cuando se alcance el límite, salvo desbloqueo autorizado.
- **RF-24:** El empleado debe poder solicitar un desbloqueo.
- **RF-25:** RR. HH. debe poder aprobar o rechazar una solicitud de desbloqueo.
- **RF-26:** El curso debe marcarse como completado cuando el empleado revise el contenido obligatorio y apruebe la evaluación.

### Certificados y notificaciones

- **RF-27:** El sistema debe generar automáticamente un certificado al completar un curso.
- **RF-28:** El certificado debe quedar asociado al empleado y al curso completado.
- **RF-29:** El empleado debe poder consultar y descargar sus certificados.
- **RF-30:** El sistema debe notificar por correo y mediante bandeja interna las asignaciones, los resultados relevantes, la disponibilidad de certificados y las decisiones sobre cursos enviados a revisión.
- **RF-31:** El sistema debe registrar el estado de entrega de las notificaciones sin convertir el envío en requisito para completar un curso.

### Usuarios, permisos e integración

- **RF-32:** El sistema debe soportar los roles Empleado, Instructor y Administrador de RR. HH.
- **RF-33:** El sistema debe autenticar usuarios mediante cuentas propias de SkillHub.
- **RF-34:** El sistema debe permitir recuperación de contraseña mediante correo corporativo.
- **RF-35:** El sistema debe consultar mediante API síncrona el nombre, área, rol y estado del empleado.
- **RF-36:** El sistema de empleados debe ser la fuente maestra de los datos organizacionales.
- **RF-37:** SkillHub no debe permitir que usuarios modifiquen manualmente los datos maestros sincronizados.
- **RF-38:** El sistema debe impedir operaciones de usuarios cuyo estado externo indique que ya no están activos.

### Reportes y auditoría

- **RF-39:** RR. HH. debe poder consultar cobertura, finalización y resultados.
- **RF-40:** Los reportes deben poder filtrarse al menos por curso, área, rol y periodo.
- **RF-41:** Cada empleado debe poder consultar sus propios resultados.
- **RF-42:** RR. HH. debe poder consultar resultados individuales de la organización.
- **RF-43:** El sistema debe registrar en una bitácora las asignaciones y los eventos de certificados.

## 7. Reglas de negocio

- **RN-01:** Solo los cursos publicados pueden aparecer como disponibles para empleados.
- **RN-02:** La publicación requiere aprobación de RR. HH.
- **RN-03:** Un curso devuelto por RR. HH. vuelve a estado borrador y conserva el motivo de devolución.
- **RN-04:** Un empleado puede inscribirse voluntariamente solo en cursos configurados para catálogo.
- **RN-05:** Una asignación obligatoria puede dirigirse a empleados, áreas o roles.
- **RN-06:** La finalización requiere revisar todos los contenidos obligatorios y aprobar la evaluación.
- **RN-07:** La calificación mínima y el límite de intentos provienen de la política corporativa.
- **RN-08:** Agotar intentos sin aprobar impide continuar hasta que RR. HH. autorice un desbloqueo.
- **RN-09:** El certificado se genera únicamente cuando el curso queda completado.
- **RN-10:** Cada empleado solo puede consultar sus propios resultados, salvo RR. HH.
- **RN-11:** Los datos de nombre, área, rol y estado del empleado pertenecen al sistema externo.

## 8. Requisitos no funcionales iniciales

### Seguridad y privacidad

- **RNF-01:** Las contraseñas deben almacenarse usando un mecanismo de hash seguro; nunca en texto plano.
- **RNF-02:** La recuperación de contraseña debe utilizar el correo corporativo registrado.
- **RNF-03:** El sistema debe aplicar autorización por rol para proteger cursos, resultados, certificados y reportes.
- **RNF-04:** Los resultados individuales no deben quedar expuestos a otros empleados ni a instructores.
- **RNF-05:** El sistema debe proteger los documentos adjuntos contra acceso no autorizado.
- **RNF-06:** La bitácora debe registrar al menos asignaciones y eventos de certificados, con actor y fecha.

### Rendimiento y capacidad

- **RNF-07:** El sistema debe soportar una población inicial aproximada de 1.000 empleados.
- **RNF-08:** Debe soportar campañas periódicas con aumentos previsibles de accesos y notificaciones.
- **RNF-09:** El volumen inicial considerado es de hasta 100 cursos, con varios documentos por curso.
- **RNF-10:** Los tiempos objetivo de respuesta y la cantidad máxima de usuarios simultáneos quedan pendientes de validación técnica.

### Disponibilidad y recuperación

- **RNF-11:** El MVP debe estar disponible durante el horario laboral acordado.
- **RNF-12:** Se permiten mantenimientos planificados fuera del horario laboral.
- **RNF-13:** Deben realizarse respaldos diarios.
- **RNF-14:** La recuperación inicial puede ser manual.
- **RNF-15:** RPO y RTO formales quedan pendientes de definir con el responsable operativo.

### Operación y despliegue

- **RNF-16:** El MVP debe poder desplegarse on-premise dentro de la infraestructura de la empresa.
- **RNF-17:** La integración con el sistema de empleados debe tolerar indisponibilidad temporal del sistema externo y comunicar el error sin corromper operaciones locales.
- **RNF-18:** Los envíos de notificaciones deben poder reintentarse sin duplicar indebidamente el efecto de una operación.

## 9. Dependencias y supuestos

- La empresa dispone de un sistema de empleados accesible mediante API síncrona.
- La API externa puede identificar empleados, áreas, roles y estado activo.
- Los usuarios tienen un correo corporativo disponible para recuperación y notificaciones.
- RR. HH. dispone de una política corporativa de calificación mínima y máximo de intentos.
- La infraestructura on-premise puede alojar la aplicación y el almacenamiento de documentos.
- El alcance inicial no requiere integrarse con un proveedor externo de identidad.
- Los cursos publicados no requieren versionado avanzado durante el MVP.

## 10. Decisiones pendientes

- Definir los campos obligatorios de un curso antes de enviarlo a revisión.
- Definir el formato y tamaño máximo de documentos adjuntos.
- Definir las clases de preguntas soportadas en las evaluaciones.
- Definir la política exacta de desbloqueo y la cantidad de intentos adicionales.
- Definir si una asignación puede cancelarse o modificarse después de iniciada.
- Definir los tiempos objetivo de respuesta y el máximo de usuarios simultáneos.
- Definir RPO y RTO con el responsable de operación.
- Definir el formato de los certificados y su validez.
- Definir el contrato de la API del sistema de empleados.
- Definir cómo se gestionan cursos publicados que requieren correcciones posteriores.

## 11. Criterios de aceptación del MVP

- Un instructor puede crear un curso con contenido mixto y enviarlo a revisión.
- RR. HH. puede devolver el curso con un motivo o aprobarlo y publicarlo.
- Un curso publicado puede aparecer en el catálogo, ser asignado o ambas cosas.
- Un empleado puede revisar el contenido y presentar una evaluación con intentos limitados.
- El sistema impide completar el curso si no se cumplen las condiciones definidas.
- RR. HH. puede desbloquear nuevos intentos mediante una solicitud registrada.
- Al completar un curso, se genera un certificado descargable.
- Los empleados reciben notificaciones por correo y en la bandeja interna.
- RR. HH. puede consultar cobertura, finalización y resultados por filtros básicos.
- Los datos organizacionales de los empleados provienen del sistema externo.
- Los permisos impiden que instructores o empleados vean resultados no autorizados.
- La solución puede desplegarse on-premise y recuperarse desde respaldos diarios.
