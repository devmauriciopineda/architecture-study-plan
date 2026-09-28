# Semana 11. Toma de decisiones arquitectónicas

## Propósito

Aprender a estructurar, justificar y comunicar decisiones de arquitectura de forma explícita. Una buena decisión no es la que evita toda duda, sino la que deja claro qué problema se resuelve, qué alternativas se evaluaron, qué supuestos se aceptaron, qué riesgos permanecen y cuándo conviene revisarla.

## Arquitectura como disciplina de decisión

Una decisión arquitectónica determina una propiedad importante de la estructura, comportamiento, despliegue, datos, comunicación u operación de un sistema. Puede elegir un límite, una forma de persistencia, un mecanismo de integración, una estrategia de recuperación o una restricción tecnológica. No toda decisión de implementación necesita el mismo nivel de registro; una decisión es arquitectónica cuando condiciona muchas partes del sistema o resulta costosa de cambiar.

La arquitectura no consiste en dibujar una solución final, sino en gestionar decisiones relacionadas con objetivos y restricciones. Una decisión puede ser correcta para el contexto actual y dejar de serlo cuando cambian la carga, el equipo, el presupuesto o los requisitos. Por eso debe expresar su contexto y sus condiciones de vigencia.

## Architecture Decision Records

Un **Architecture Decision Record (ADR)** es un documento breve que registra una decisión arquitectónica, su contexto, sus alternativas y sus consecuencias. Un ADR no es una especificación completa ni una orden permanente: conserva el razonamiento que permite entender por qué se eligió una opción y qué tendría que cambiar para revisarla.

Un ADR suele incluir título, estado, contexto, drivers, restricciones, supuestos, alternativas consideradas, decisión, consecuencias, riesgos y fecha o condición de revisión. El formato puede variar, pero debe permitir que una persona que no participó en la conversación entienda la conclusión sin reconstruirla a partir de mensajes antiguos.

El estado de un ADR puede ser propuesto, aceptado, reemplazado o descartado. Marcarlo como reemplazado es preferible a borrarlo: las decisiones históricas explican contratos, datos o comportamientos que todavía existen. Un ADR puede enlazar a requisitos, métricas, pruebas o decisiones relacionadas, pero no debe ocultar su conclusión detrás de enlaces.

## Decision drivers

Los **decision drivers** o impulsores de decisión son los objetivos y propiedades que hacen que una alternativa sea preferible. Pueden incluir latencia, disponibilidad, seguridad, costo, capacidad de evolución, simplicidad, tiempo de entrega o cumplimiento. Un driver debe formularse de manera suficientemente concreta para distinguir opciones.

“Usar tecnología moderna” no es un driver útil. “Reducir el tiempo de respuesta del percentil alto bajo la carga de campaña” o “permitir recuperación dentro del objetivo acordado” sí orientan la evaluación. Los drivers pueden tener prioridades distintas; registrar esa prioridad evita que una opción gane por una característica secundaria mientras incumple una necesidad principal.

Los drivers provienen de requisitos, restricciones, riesgos y estrategia de producto. Deben separarse de soluciones prematuras. Si el driver es disponibilidad, no se ha decidido todavía que la respuesta sea añadir réplicas; esa es una alternativa que debe evaluarse.

## Restricciones y supuestos

Una **restricción** es una condición que limita las opciones disponibles. Puede ser técnica, económica, legal, organizacional o temporal: una infraestructura existente, un presupuesto, un sistema externo obligatorio, una política de seguridad o una fecha de entrega. Una restricción no es una preferencia; si puede negociarse, debe indicarse quién puede cambiarla y a qué costo.

Un **supuesto** es una afirmación tomada como cierta para poder decidir, aunque todavía no haya sido verificada por completo. Por ejemplo, se puede asumir una cantidad de usuarios simultáneos o la estabilidad de un contrato externo. Los supuestos hacen visible la incertidumbre; si permanecen ocultos, una decisión parece más segura de lo que realmente es.

Cada supuesto debe tener una fuente, un nivel de confianza y una forma de validación cuando sea relevante. Si el supuesto cambia, no siempre hay que descartar el ADR, pero sí revisar si cambian los drivers, los riesgos o la alternativa recomendada.

## Alternativas y decisión

Una **alternativa** es una opción realista que podría resolver el problema dentro de las restricciones. Comparar una opción elegida contra una caricatura de las demás no es análisis. Conviene incluir la opción de no cambiar, una alternativa simple, una alternativa intermedia y, solo cuando exista una razón, una opción de mayor capacidad o control.

La evaluación debe describir beneficios, costos, riesgos y consecuencias de cada alternativa. Una alternativa descartada también puede ser útil en el futuro; registrar por qué se descartó evita repetir discusiones o reintroducirla sin atender su problema original.

La **decisión** es la alternativa seleccionada y el alcance exacto de lo que se adopta. Debe evitar frases ambiguas como “usar una solución escalable”. Es mejor indicar qué capacidad se separa, qué contrato se mantiene, qué propiedad se prioriza y bajo qué condiciones se revisará.

## Consecuencias y principios de arquitectura

Una **consecuencia** es un efecto aceptado de una decisión, positivo o negativo. Puede afectar desarrollo, rendimiento, costo, seguridad, operación, datos, experiencia de usuario o evolución. Una consecuencia no es necesariamente un fallo: aceptar despliegue conjunto puede ser razonable si reduce coordinación en el MVP, siempre que se reconozca la menor independencia de despliegue.

Un **principio de arquitectura** es una regla general que orienta muchas decisiones, como “la fuente maestra debe tener un propietario único”, “las dependencias externas deben estar aisladas” o “la información sensible debe tener acceso mínimo”. Un principio no elige por sí solo una tecnología; proporciona consistencia para comparar opciones.

Los principios deben ser pocos, comprensibles y aplicables. Un principio que exige simultáneamente máxima disponibilidad, máxima consistencia, mínimo costo y mínima complejidad no ayuda a decidir. Cuando dos principios entran en tensión, el ADR debe mostrar cuál prevalece en el contexto y por qué.

## Análisis de riesgos

Un **riesgo** es un evento incierto que puede producir un impacto negativo o impedir alcanzar un objetivo. El análisis de riesgos identifica causa, probabilidad, impacto, señales tempranas, mitigación y plan de contingencia. No todos los riesgos se eliminan; algunos se reducen, se transfieren, se aceptan o se evitan.

La probabilidad y el impacto no deben convertirse en números decorativos. Una escala cualitativa es suficiente si define qué significa cada nivel y orienta una acción. Un riesgo de baja probabilidad y alto impacto puede justificar una prueba de recuperación; uno frecuente y menor puede resolverse con una mejora operativa.

La mitigación reduce la probabilidad o el impacto antes del evento. La contingencia define qué hacer si el evento ocurre. Por ejemplo, aislar una dependencia reduce propagación; un modo degradado o un proceso manual define la respuesta posterior. Un riesgo residual es el que permanece después de las medidas aceptadas y debe quedar visible en el ADR.

## Deuda técnica

La **deuda técnica** es una obligación futura creada por una decisión que deja un trabajo pendiente, una limitación o una solución menos mantenible. Puede ser deliberada, cuando se registra para obtener aprendizaje o entregar antes, o accidental, cuando surge sin reconocimiento.

En un ADR, la deuda técnica debe describirse junto con su interés: impacto acumulado, condición que la vuelve relevante y forma de pago. “Revisar más adelante” no es un plan. Una condición verificable, como superar un volumen, alcanzar un costo o cambiar un contrato, permite saber cuándo actuar.

No toda simplicidad inicial es deuda. Una solución sencilla puede ser la decisión adecuada si cumple los objetivos y deja un camino razonable de evolución. Se convierte en deuda cuando el equipo conoce una limitación, depende de una excepción o posterga una propiedad necesaria sin registrar su consecuencia.

## Reversibilidad y decisiones de una o dos vías

La **reversibilidad** es la facilidad, costo y riesgo de deshacer o cambiar una decisión. Una decisión reversible puede probarse y reemplazarse con impacto limitado. Una decisión poco reversible puede comprometer datos, contratos, identidad, migración, capacitación o dependencia de proveedor.

Una **decisión de dos vías** es aquella cuyo cambio posterior es relativamente sencillo y seguro. Conviene decidirla con rapidez razonable, medir y aprender. Una **decisión de una vía** es difícil o costosa de revertir; requiere más análisis, pruebas y participación antes de comprometerse.

La reversibilidad no es binaria. Una base de datos puede cambiarse si los datos son exportables y existe una migración probada, pero no si el formato propietario está disperso en todo el código. Un proveedor puede reemplazarse si hay un contrato interno y una ruta de salida, aunque la migración siga costando tiempo.

Cuando una decisión es poco reversible, se puede reducir el compromiso mediante una prueba, un adaptador, un límite de contexto o una política de exportación. El objetivo no es evitar decidir, sino comprar información antes de cerrar opciones costosas.

## Método para redactar un ADR

Aplica esta secuencia:

1. **Formula el problema.** Describe la decisión que debe tomarse y qué queda fuera.
2. **Reúne contexto.** Conecta requisitos, drivers, restricciones, supuestos y datos disponibles.
3. **Construye alternativas.** Incluye una opción simple y explica por qué las opciones son viables.
4. **Evalúa consecuencias.** Compara beneficios, costos, riesgos, deuda, operación, seguridad y evolución.
5. **Clasifica la reversibilidad.** Identifica qué puede cambiarse después y qué requiere compromiso duradero.
6. **Decide.** Declara una alternativa, su alcance y los principios que la respaldan.
7. **Registra la revisión.** Define métricas, evento, fecha o cambio de supuesto que puede reemplazar el ADR.

Un ADR debe ser firme sin fingir certeza. Puede decir “aceptado con el supuesto de que la carga no supera X” o “propuesto hasta validar el contrato”. La transparencia sobre incertidumbre mejora la decisión porque indica dónde invertir la siguiente investigación.

## Cierre

Registrar decisiones convierte conversaciones dispersas en conocimiento arquitectónico reutilizable. Los ADR conectan drivers, alternativas, restricciones, riesgos y consecuencias; los principios aportan coherencia y la reversibilidad ayuda a decidir cuánto análisis merece cada compromiso. En la semana 12 se estudiará cómo evolucionar la arquitectura, revisando estas decisiones cuando cambien los requisitos, la carga, los riesgos o el contexto del producto.