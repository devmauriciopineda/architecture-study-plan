# Semana 2. Requirements & Constraints: de necesidades a condiciones arquitectónicas

## Propósito del capítulo

Una arquitectura no responde únicamente a la pregunta "¿qué funciones tendrá el sistema?". También debe responder bajo qué condiciones debe operar, qué riesgos debe controlar, qué volumen debe soportar, qué información debe proteger y qué límites no puede cruzar.

Este capítulo presenta un método para transformar necesidades de negocio y expresiones ambiguas en requisitos y restricciones suficientemente concretos para orientar decisiones de diseño. El objetivo no es producir una especificación perfecta ni congelar el futuro, sino reducir ambigüedad, hacer explícitos los supuestos y proporcionar criterios verificables para comparar alternativas.

## 1. Del problema de negocio al requisito arquitectónico

Un **business requirement**, o requisito de negocio, es una condición que expresa un objetivo, resultado o necesidad de la organización. Describe por qué una capacidad importa, pero normalmente no determina todavía cómo debe implementarse. Por ejemplo, una organización puede necesitar reducir el tiempo de incorporación de nuevos empleados o demostrar que determinadas capacitaciones fueron completadas.

Un **requisito** es una condición o capacidad que el sistema debe satisfacer para contribuir a un objetivo. Un requisito útil conecta una necesidad con un comportamiento o propiedad observable. La cadena de razonamiento suele ser:

1. Una parte interesada tiene una necesidad o un objetivo.
2. El producto debe producir un resultado para atenderlo.
3. El sistema necesita capacidades funcionales.
4. Esas capacidades deben operar con propiedades y límites determinados.
5. La arquitectura debe ofrecer mecanismos que hagan posible cumplirlos.

Si se salta directamente de una necesidad a una tecnología, se pierde la relación entre decisión y objetivo. Si se escribe una lista de funciones sin contexto, no se sabe qué priorizar cuando dos propiedades entran en conflicto. Los requisitos sirven precisamente para conservar esa relación.

Un requisito debe expresar una condición que pueda discutirse y, en lo posible, verificarse. Debe indicar quién necesita qué, bajo qué condiciones y con qué resultado. Las palabras "fácil", "rápido", "seguro" o "escalable" pueden ser útiles como primera señal, pero no son especificaciones suficientes hasta que se define su significado operacional.

## 2. Functional requirements

Un **functional requirement**, o requisito funcional, describe una capacidad, comportamiento o transformación que el sistema debe ofrecer. Responde a la pregunta: **¿qué debe hacer el sistema?**

Ejemplos genéricos:

- permitir que un usuario registre una solicitud;
- validar que una operación cumpla una regla;
- generar un informe a partir de información almacenada;
- notificar un resultado a un actor;
- rechazar una operación no autorizada;
- conservar la evidencia de una transacción.

Un requisito funcional no se limita a una pantalla o a una llamada de API. Puede incluir reglas de negocio, transiciones de estado, cálculos, permisos, eventos y efectos persistentes. Para formularlo con precisión conviene especificar:

- actor o proceso que inicia la acción;
- precondiciones relevantes;
- información de entrada;
- comportamiento esperado;
- resultado y estado final;
- errores o excepciones importantes;
- permisos y trazabilidad requeridos.

Una buena formulación evita incluir una solución prematura. "El sistema debe permitir consultar el estado de una solicitud" expresa una capacidad. "Debe usar una tabla con una columna `status`" prescribe una implementación que quizá todavía no corresponde decidir.

## 3. Non-functional requirements

Un **non-functional requirement**, o requisito no funcional, describe una propiedad, nivel de servicio o condición de operación del sistema, en lugar de una capacidad de negocio aislada. Responde a la pregunta: **¿con qué calidad, límite o comportamiento operativo debe funcionar?**

Son requisitos no funcionales la protección de datos, el tiempo de respuesta, la disponibilidad, la capacidad, la auditabilidad, la mantenibilidad y el costo máximo de operación. No son secundarios: pueden determinar la estructura de la solución tanto como una función principal.

Un requisito no funcional debe ser verificable mediante una métrica, umbral, escenario o condición. Por ejemplo, "la aplicación debe ser segura" no define una prueba. Una formulación más útil podría indicar qué actores pueden acceder a determinados datos, qué eventos deben auditarse y qué controles deben existir.

Los requisitos funcionales y no funcionales se condicionan mutuamente. Una operación de consulta puede requerir una respuesta rápida; un proceso de autenticación debe cumplir controles de seguridad; un informe puede necesitar consistencia suficiente para que sus resultados sean confiables. Separarlos ayuda a analizarlos, pero el diseño debe tratarlos como un conjunto.

## 4. Quality attributes

Un **quality attribute**, o atributo de calidad, es una propiedad medible o evaluable que caracteriza cómo se comporta un sistema. Los atributos de calidad suelen agrupar requisitos no funcionales relacionados.

Algunos atributos frecuentes son:

- **Rendimiento:** capacidad de responder y procesar trabajo dentro de tiempos aceptables.
- **Disponibilidad:** proporción de tiempo o solicitudes durante la cual el sistema está operativo y puede prestar su función.
- **Confiabilidad:** capacidad de funcionar correctamente durante un periodo y bajo condiciones definidas.
- **Seguridad:** capacidad de preservar confidencialidad, integridad, autenticidad y autorización.
- **Escalabilidad:** capacidad de mantener propiedades aceptables cuando aumenta la carga o el tamaño del sistema.
- **Mantenibilidad:** facilidad y costo relativo de corregir, modificar o extender el sistema.
- **Observabilidad:** capacidad de inferir el estado interno a partir de señales externas, como registros, métricas y trazas.
- **Auditabilidad:** capacidad de demostrar qué ocurrió, cuándo, por qué y quién participó.

Un atributo no es un número aislado. Debe describirse mediante un escenario: estímulo, contexto, respuesta esperada y medida. Por ejemplo, ante una consulta de catálogo en condiciones normales y con una carga determinada, el sistema podría responder al percentil 95 en menos de un umbral acordado. Sin contexto, el número carece de significado.

Los atributos compiten. Más redundancia puede mejorar disponibilidad y aumentar costo; controles adicionales pueden fortalecer seguridad y añadir latencia; una estructura muy flexible puede dificultar mantenibilidad. Por eso se deben priorizar según los objetivos y riesgos del producto.

## 5. Constraints

Una **constraint**, o restricción, es una condición que limita las soluciones aceptables. A diferencia de un requisito, que describe algo que el sistema debe hacer o proporcionar, una restricción limita cómo, dónde, cuándo o con qué recursos puede construirse y operarse.

Ejemplos de restricciones son una infraestructura disponible, una regulación, un presupuesto, una fecha límite, una tecnología impuesta, una política organizacional o la necesidad de interoperar con un sistema existente. Una restricción puede ser externa al equipo, pero sigue teniendo consecuencias arquitectónicas.

Conviene clasificar las restricciones para no confundir su fuerza:

- **Dura:** su incumplimiento hace inaceptable la solución.
- **Blanda:** expresa una preferencia o prioridad que puede negociarse.
- **Actual:** ya existe y debe respetarse.
- **Propuesta:** se considera, pero requiere validación.
- **De diseño:** limita la forma de construir.
- **De operación:** limita la forma de desplegar, mantener o usar.

Toda restricción debe registrar su origen, alcance, grado de certeza y consecuencia. "Debe desplegarse en infraestructura propia" es más útil si se aclara quién lo exige, qué recursos están disponibles y qué capacidades de operación implica. Las restricciones no desaparecen por no documentarlas; solo se vuelven riesgos ocultos.

## 6. Assumptions

Un **assumption**, o supuesto, es una afirmación que se toma como válida para planificar o diseñar, aunque todavía no haya sido confirmada completamente. Los supuestos permiten avanzar bajo incertidumbre, pero crean riesgo si resultan falsos.

Ejemplos: suponer que una API externa estará disponible durante el horario laboral, que los usuarios tendrán correo corporativo o que una población crecerá dentro de cierto rango. Un supuesto no es un hecho ni una restricción: es una hipótesis de trabajo.

Cada supuesto importante debería indicar:

1. Qué se está asumiendo.
2. En qué evidencia se basa.
3. Qué decisión habilita.
4. Cómo y cuándo se validará.
5. Qué impacto tendría que sea falso.

La diferencia entre restricción y supuesto es especialmente importante. "La organización debe conservar registros durante siete años" es una obligación si está respaldada por una política aplicable. "Probablemente no necesitaremos consultar registros antiguos" es un supuesto. Tratar el segundo como hecho puede producir una arquitectura difícil de corregir.

## 7. Workload characteristics

Las **workload characteristics**, o características de carga de trabajo, describen el trabajo que el sistema recibe y procesa. Incluyen volumen, frecuencia, distribución temporal, tamaño de solicitudes y respuestas, proporción entre lecturas y escrituras, concurrencia, duración de procesos y patrones de picos.

Una carga no se define solamente por el número total de usuarios. Dos sistemas con la misma población pueden tener comportamientos muy distintos: uno puede recibir solicitudes uniformemente y otro concentrarlas en campañas, cierres de mes o eventos periódicos.

Para describir una carga conviene preguntar:

- ¿Cuántas entidades, operaciones o mensajes existen?
- ¿Cuántas operaciones ocurren por segundo, minuto, día o periodo?
- ¿Qué proporción corresponde a lectura, escritura, búsqueda o procesamiento?
- ¿Cuántos usuarios u operaciones pueden concurrir?
- ¿Cuál es el tamaño típico y máximo de los datos?
- ¿Hay picos previsibles, ráfagas o crecimiento estacional?
- ¿Qué operaciones son interactivas y cuáles pueden procesarse después?

Las características de carga conectan requisitos de negocio con decisiones técnicas. Una campaña que genera muchas notificaciones simultáneas puede exigir colas o control de capacidad; un conjunto pequeño de consultas críticas puede priorizar baja latencia y consistencia inmediata.

## 8. Scale

La **scale**, o escala, es la magnitud del sistema y de la carga que debe soportar. Puede medirse en usuarios, datos, operaciones, transacciones, conexiones, regiones, equipos, archivos o tiempo de retención.

La escala tiene varias dimensiones:

- **Escala de datos:** cuánto se almacena y cómo crece.
- **Escala de tráfico:** cuántas solicitudes o mensajes se procesan.
- **Escala de concurrencia:** cuántas operaciones ocurren al mismo tiempo.
- **Escala organizacional:** cuántos equipos, roles, unidades o clientes participan.
- **Escala temporal:** cuánto tiempo deben conservarse los datos y cómo cambia la carga.

La escala actual y la escala objetivo no son lo mismo. La primera ayuda a construir una solución proporcionada; la segunda permite evitar decisiones que bloqueen una evolución razonablemente previsible. Diseñar para una escala hipotética enorme puede introducir complejidad, costo y riesgo innecesarios. Ignorar un crecimiento cercano y conocido puede obligar a una migración prematura.

Una afirmación de escala debe incluir un horizonte y una métrica. "Muchos usuarios" no permite calcular capacidad; "hasta cierta población, con picos de determinado volumen durante campañas" sí puede alimentar una estimación inicial.

## 9. Latency

La **latency**, o latencia, es el tiempo transcurrido entre un estímulo y la disponibilidad de su respuesta o resultado. En una operación distribuida puede incluir espera de red, procesamiento, acceso a datos, colas y serialización.

La latencia percibida por un usuario no siempre coincide con la latencia de un componente. Una operación puede invocar varias dependencias secuencialmente y acumular sus tiempos. También puede devolver una aceptación rápida y completar el trabajo de forma asíncrona; en ese caso hay que distinguir el tiempo de confirmación del tiempo hasta el resultado final.

Las métricas de latencia deben definir el contexto y la distribución. El promedio puede ocultar experiencias muy lentas; los percentiles muestran qué ocurre con una proporción concreta de solicitudes. Una especificación útil indica operación, carga, percentil, umbral y condiciones de medición.

La latencia es una decisión de producto además de una propiedad técnica. Una búsqueda interactiva, una generación de informe y un envío de notificación pueden admitir límites diferentes. Intentar que todos los procesos tengan el mismo objetivo suele desperdiciar recursos o ignorar necesidades reales.

## 10. Availability

La **availability**, o disponibilidad, es la probabilidad o proporción de tiempo durante la cual un sistema está operativo y puede prestar una función definida. No significa que toda operación sea perfecta ni que el sistema no presente degradación.

La disponibilidad debe expresarse con una ventana y un alcance: por ejemplo, una función crítica durante horario laboral, un servicio completo durante un mes o una API determinada. Un porcentaje aislado no revela qué fallos se toleran ni cuánto tiempo de interrupción implica.

También hay que distinguir disponibilidad de confiabilidad. La disponibilidad pregunta si el sistema está utilizable en un momento; la confiabilidad se refiere a la continuidad del funcionamiento correcto durante un periodo. Un sistema puede responder siempre, pero devolver resultados incorrectos, o puede fallar brevemente y recuperarse de forma confiable.

La disponibilidad depende de la arquitectura y de la operación: redundancia, respaldos, mantenimiento, recuperación, dependencias externas y procedimientos humanos. Aumentarla casi siempre implica costos y complejidad adicionales, por lo que el objetivo debe derivarse de la importancia de cada capacidad.

## 11. Consistency

La **consistency**, o consistencia, es la propiedad que define qué relación deben mantener las observaciones de los datos después de operaciones concurrentes, distribuidas o separadas en el tiempo. En términos prácticos, indica qué versión de la información puede ver cada consumidor y bajo qué condiciones.

La consistencia no tiene un único significado universal. Puede referirse a reglas dentro de una transacción, a la visibilidad de una escritura en distintos nodos o a la coherencia entre representaciones derivadas. Por eso el requisito debe decir qué dato necesita qué garantía y durante cuánto tiempo.

La **consistencia fuerte** exige que una lectura observe una versión acorde con las operaciones confirmadas según un orden definido. La **consistencia eventual** permite que distintas copias difieran temporalmente, con la expectativa de converger si no aparecen nuevas actualizaciones. Entre ambas existen garantías y modelos adicionales.

La consistencia es una decisión de negocio. Un dato puede tolerar retraso si se usa para una vista informativa, pero no si controla autorización, saldo, identidad o una transición irreversible. Aumentar la consistencia puede elevar latencia, reducir disponibilidad o complicar la coordinación; relajarla exige manejar estados intermedios y conflictos.

## 12. Security

La **security**, o seguridad, es la capacidad de proteger sistemas y datos frente a acceso, uso, modificación, divulgación o interrupción no autorizados. No es una característica única, sino un conjunto de objetivos y controles.

Sus propiedades fundamentales incluyen:

- **Confidencialidad:** solo acceden a la información las entidades autorizadas.
- **Integridad:** los datos y operaciones no se modifican de forma indebida.
- **Disponibilidad:** los usuarios autorizados pueden utilizar el sistema cuando corresponde.
- **Autenticidad:** se puede verificar la identidad o el origen de una entidad o mensaje.
- **Autorización:** se determina qué acciones puede realizar una identidad autenticada.
- **Trazabilidad:** las acciones relevantes pueden asociarse a un actor y momento.

Un requisito de seguridad debe partir de activos, amenazas, actores y consecuencias. No basta con nombrar una herramienta. La especificación debe indicar qué se protege, contra qué escenario, con qué control y cómo se comprobará. También debe considerar secretos, sesiones, privilegios, dependencias, archivos, registros y procesos operativos.

La seguridad debe analizarse desde el inicio porque muchas decisiones posteriores son costosas de cambiar. A la vez, los controles deben ser proporcionales al riesgo: más controles no garantizan automáticamente mejor seguridad si introducen errores, complejidad o falsas expectativas.

## 13. Cost constraints

Las **cost constraints**, o restricciones de costo, son límites sobre los recursos económicos, humanos y operativos que pueden dedicarse a construir, ejecutar y mantener una solución. El costo no es únicamente el precio de infraestructura: incluye desarrollo, licencias, soporte, operación, capacitación, migraciones, incidentes y costo de oportunidad.

Conviene distinguir:

- **Costo inicial:** análisis, construcción, adquisición y puesta en marcha.
- **Costo operativo:** infraestructura, almacenamiento, red, soporte y monitoreo.
- **Costo de cambio:** esfuerzo necesario para corregir, extender o migrar.
- **Costo de fallo:** pérdida, interrupción, incumplimiento o recuperación tras un incidente.

Una restricción de costo debe indicar el periodo, el alcance y los elementos incluidos. "Debe ser barato" no permite decidir; un presupuesto máximo mensual, una capacidad de equipo o una fecha límite sí puede compararse con alternativas.

El costo se relaciona con los atributos de calidad. Una disponibilidad superior, una latencia menor o una mayor capacidad pueden requerir redundancia, infraestructura o personal especializado. La decisión arquitectónica consiste en alcanzar el nivel necesario, no el máximo imaginable, dentro de las restricciones aceptadas.

## 14. Especificación, priorización y trazabilidad

La **trazabilidad** es la capacidad de relacionar una necesidad con sus requisitos, decisiones, implementación y evidencia de verificación. Permite saber por qué existe una condición y qué consecuencias tendría cambiarla.

Una ficha de requisito puede incluir identificador, origen, descripción, tipo, prioridad, métrica o criterio de aceptación, dependencias, supuestos y estado de validación. No es necesario convertir toda la documentación en un proceso burocrático; el nivel de detalle debe ser proporcional al riesgo y a la complejidad.

La **prioridad** indica la importancia relativa de una condición para el producto y el momento actual. Una forma práctica de priorizar es distinguir lo imprescindible para que el sistema sea válido, lo importante para que sea usable y lo deseable para una evolución posterior. También se puede ponderar impacto, urgencia, riesgo y costo.

Cuando dos requisitos compiten, no se resuelve el conflicto ocultándolo. Se explicita el escenario, se identifican las partes interesadas, se estima el impacto y se acuerda qué propiedad domina. Así, la arquitectura se convierte en una respuesta razonada a objetivos y restricciones, no en una colección de preferencias técnicas.

## 15. Método práctico de análisis

Para transformar necesidades en condiciones arquitectónicas se puede seguir esta secuencia:

1. **Recoger objetivos:** identificar qué resultado necesita cada parte interesada y por qué.
2. **Separar tipos de condición:** distinguir requisitos funcionales, atributos de calidad, restricciones y supuestos.
3. **Describir escenarios:** indicar estímulo, contexto, respuesta, medida y criterio de aceptación.
4. **Caracterizar la carga:** registrar volumen, frecuencia, concurrencia, tamaño y picos.
5. **Definir prioridades:** señalar qué es obligatorio, negociable, futuro o pendiente de validar.
6. **Identificar conflictos:** analizar compromisos entre latencia, disponibilidad, consistencia, seguridad, costo y complejidad.
7. **Registrar incertidumbre:** documentar supuestos, decisiones pendientes y riesgos de que sean falsos.
8. **Validar con las partes interesadas:** comprobar que las métricas representan necesidades reales.
9. **Revisar durante la evolución:** actualizar el conjunto cuando cambien el negocio, la carga o las restricciones.

Este análisis no produce automáticamente una arquitectura. Produce el marco con el que una arquitectura puede evaluarse. Una propuesta es defendible cuando se puede explicar qué requisito satisface, bajo qué escenario, con qué costo y qué compromisos introduce.

## Cierre

Los requisitos y las restricciones son el lenguaje que conecta el propósito del producto con la arquitectura. Los requisitos funcionales dicen qué capacidad debe existir; los atributos de calidad describen cómo debe comportarse; las restricciones limitan el espacio de soluciones; los supuestos hacen explícita la incertidumbre; y las características de carga permiten dimensionar las expectativas.

La pregunta central de esta semana es: **¿qué debe hacer el sistema, bajo qué condiciones, con qué volumen y dentro de qué límites?** Responderla con precisión evita diseñar para necesidades imaginarias o descubrir demasiado tarde propiedades que debían guiar la arquitectura desde el principio. La siguiente semana utilizará estas condiciones para establecer límites, responsabilidades y modularidad.
