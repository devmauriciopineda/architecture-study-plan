# Semana 10. Costos, rendimiento y trade-offs arquitectónicos

## Propósito

Aprender a evaluar alternativas de arquitectura cuando ninguna maximiza todas las propiedades a la vez. El objetivo no es encontrar una solución perfecta, sino relacionar objetivos, restricciones, costos, riesgos y consecuencias para tomar una decisión defendible y revisable.

## Decidir bajo trade-offs

Un **trade-off** es un compromiso en el que mejorar una propiedad implica aceptar un costo, una limitación o un riesgo en otra. Una arquitectura puede reducir latencia aumentando infraestructura, mejorar disponibilidad duplicando componentes o simplificar operación renunciando a cierta flexibilidad. Los trade-offs existen aunque no se documenten; hacerlos explícitos permite gobernarlos.

Una alternativa debe evaluarse contra sus **drivers**, es decir, los objetivos que hacen importante una decisión: tiempo de respuesta, disponibilidad, seguridad, costo, velocidad de entrega, capacidad de evolución o facilidad de operación. También deben registrarse restricciones, como presupuesto, habilidades, infraestructura disponible, regulación o compatibilidad con sistemas existentes.

Comparar opciones solo por características produce listas de preferencias. Compararlas por escenarios produce decisiones. Para cada alternativa pregunta qué sucede durante una carga normal, un pico, una falla, un cambio de requisito y una operación de mantenimiento. El costo es el valor total de las consecuencias, no solo el precio de compra.

## Costo frente a rendimiento

El **costo** de una solución incluye infraestructura, licencias, almacenamiento, transferencia, soporte, personal, monitoreo, seguridad, recuperación y tiempo de desarrollo. Puede ser fijo, variable o dependiente del volumen. Un costo inicial bajo puede producir un costo operativo alto; una inversión inicial mayor puede reducir trabajo repetitivo o evitar incidentes.

El **rendimiento** describe la capacidad de responder y procesar trabajo bajo una carga definida. Incluye latencia, throughput, concurrencia y uso de recursos. Mejorarlo mediante más capacidad puede ser razonable cuando existe un objetivo medible, pero sobredimensionar sin conocer la carga convierte un supuesto en gasto permanente.

El trade-off **cost vs. performance** requiere definir qué latencia o throughput necesita realmente el usuario. Una consulta interna no siempre necesita la misma respuesta que una operación interactiva crítica. Optimizar una ruta que no es cuello de botella puede aumentar complejidad sin mejorar la experiencia. Conviene medir primero, estimar el beneficio y comparar el costo por unidad de mejora.

## Costo frente a confiabilidad

La **confiabilidad** es la capacidad de operar correctamente durante un periodo; la disponibilidad es solo una de sus dimensiones. El trade-off **cost vs. reliability** aparece cuando redundancia, respaldos frecuentes, monitoreo permanente o recuperación rápida requieren recursos adicionales.

No todo dato ni toda función merece el mismo objetivo. Una interrupción breve de un reporte puede ser aceptable si las operaciones de aprendizaje continúan. En cambio, perder resultados o certificados puede exigir controles, copias y auditoría más costosos. La decisión debe vincular el gasto con el impacto de incumplir el objetivo, no con una búsqueda abstracta de máxima confiabilidad.

La confiabilidad también tiene costos de complejidad. Cada réplica, ruta alternativa o procedimiento de recuperación debe configurarse, probarse y operarse. Una redundancia mal diseñada puede fallar junto con el componente principal o introducir inconsistencias. El beneficio solo es real si se cubren dominios de fallo relevantes y se verifica la recuperación.

## Complejidad frente a escalabilidad

La **complejidad** es la cantidad de conceptos, dependencias, estados, interacciones y conocimiento necesarios para construir y operar una solución. La **escalabilidad** es su capacidad de sostener crecimiento de carga o datos manteniendo objetivos aceptables.

El trade-off **complexity vs. scalability** aparece cuando una solución distribuida, particionada o asíncrona puede crecer mejor, pero exige coordinación, observabilidad y recuperación más difíciles. Para una carga pequeña y conocida, la complejidad adicional puede superar el beneficio. Cuando la carga es variable, la separación de capacidades puede evitar que una función limite a las demás.

Escalar no siempre exige distribuir. Una consulta mejorada, una caché o una partición local pueden resolver un límite antes de introducir nuevos servicios. La decisión debe incluir el ritmo de crecimiento, la reversibilidad y la capacidad del equipo para operar la solución, no solo el volumen futuro imaginado.

## Consistencia frente a disponibilidad

La **consistencia** define qué versiones y combinaciones de datos puede observar un consumidor. La **disponibilidad** define si el sistema puede responder dentro de un comportamiento aceptable. El trade-off **consistency vs. availability** se vuelve visible durante fallos de comunicación o cuando no es posible coordinar todas las copias.

Mantener consistencia fuerte puede exigir rechazar una operación si no se puede verificar el estado correcto. Mantener disponibilidad puede permitir una respuesta con datos retrasados o una acción que se reconciliará después. Ninguna opción es universalmente superior: depende de la regla que se protege.

Un agregado analítico puede aceptar retraso; un límite de intentos, una autorización o un registro financiero normalmente necesita una garantía más fuerte. La decisión debe especificar qué lecturas pueden ser antiguas, qué operaciones se bloquean y cómo se resuelven estados intermedios. Decir “consistencia eventual” sin definir el retraso aceptable no es un contrato suficiente.

## Build frente a buy

**Build vs. buy** es la decisión entre construir una capacidad internamente o adquirirla de un proveedor, producto o servicio existente. Construir ofrece control sobre comportamiento, integración y evolución, pero exige tiempo, conocimiento, mantenimiento y responsabilidad completa. Comprar acelera el acceso a una capacidad probada, pero introduce costo recurrente, restricciones de configuración, dependencia y riesgo de migración.

La decisión debe considerar si la capacidad diferencia el producto o es una función de soporte. La lógica central del dominio suele justificar mayor control; correo, almacenamiento especializado o monitoreo pueden ser buenos candidatos para adquirir, aunque siempre deben evaluarse seguridad, contrato, recuperación y salida.

Comprar no elimina el trabajo de arquitectura. El equipo debe integrar, configurar, observar, gestionar identidad, proteger datos y responder cuando el proveedor no esté disponible. Construir tampoco garantiza flexibilidad: una solución propia puede acumular deuda y quedar sin mantenimiento.

## Managed frente a self-managed

Un servicio **managed** o gestionado es operado en parte por un proveedor. **Self-managed** significa que la organización administra directamente la capacidad, sus actualizaciones, respaldos, seguridad y recuperación. El trade-off no es solo económico: cambia quién responde ante fallos y cuánto conocimiento operativo debe conservar el equipo.

Managed puede reducir tareas rutinarias y mejorar el tiempo de provisión. Self-managed puede ofrecer control, personalización o ejecución en un entorno donde no existe una alternativa gestionada. La comparación debe incluir personal requerido, ventanas de mantenimiento, límites, observabilidad, portabilidad, cumplimiento y facilidad de restauración.

Un servicio gestionado sigue necesitando un contrato de salida y pruebas de recuperación. Una solución autogestionada sigue necesitando automatización y disciplina operativa. La decisión correcta es la que asigna cada responsabilidad a quien puede cumplirla de forma medible.

## Simplicidad frente a flexibilidad

La **simplicidad** reduce componentes, estados, decisiones y caminos de operación. La **flexibilidad** permite adaptar una solución a más variantes, consumidores, cargas o futuros requisitos. El trade-off **simplicity vs. flexibility** aparece al decidir si se diseña para el problema actual o para posibilidades todavía inciertas.

Una interfaz con menos opciones puede ser más fácil de usar, probar y evolucionar. Una solución flexible puede evitar una migración futura, pero también puede ocultar reglas y multiplicar combinaciones que deben mantenerse. La flexibilidad tiene valor cuando existe una probabilidad razonable de necesitarla y el costo de incorporarla después sería alto.

Diseñar puntos de extensión pequeños suele ser mejor que anticipar todos los futuros. Deben identificarse decisiones reversibles y no reversibles. Es preferible mantener una opción abierta mediante un contrato estable que construir desde el inicio una plataforma general sin usuarios ni carga que la justifiquen.

## Corto plazo frente a largo plazo

El trade-off **short-term vs. long-term** compara velocidad de entrega y costo inmediato con mantenibilidad, evolución, migración y operación futura. Una solución provisional puede ser válida si su alcance, límite y fecha de revisión están documentados. Se vuelve peligrosa cuando el equipo olvida que era provisional.

El largo plazo no significa construir todo por adelantado. Significa reconocer qué decisiones serán costosas de cambiar: modelo de datos, identidad, ownership, contratos externos o dependencia de proveedor. Para ellas conviene investigar y probar más. Para decisiones reversibles, una implementación sencilla puede producir aprendizaje real antes de invertir.

## Deuda técnica y complejidad operativa

La **deuda técnica** es el costo futuro creado por una decisión que acelera el presente o deja una obligación pendiente. Puede ser deliberada, como posponer una optimización con una fecha de revisión, o accidental, como duplicar lógica sin reconocerlo. La deuda no es automáticamente mala: es peligrosa cuando no se conoce su interés, su impacto ni la forma de pagarla.

La **complejidad operativa** es el esfuerzo necesario para desplegar, observar, escalar, asegurar, mantener y recuperar un sistema. Dos soluciones con el mismo costo de infraestructura pueden tener costos operativos muy distintos. Cada proceso adicional, contrato, cola, réplica o proveedor añade alarmas, procedimientos y posibles estados incompletos.

Una alternativa puede justificar complejidad si reduce un riesgo importante o habilita un objetivo necesario. Debe incluir presupuesto de operación: quién responde de noche, cómo se diagnostica una falla, cómo se prueba una restauración y qué ocurre durante una actualización. Si esas respuestas no existen, el precio real de la arquitectura está incompleto.

## Vendor lock-in

El **vendor lock-in** es la dependencia de un proveedor que hace costoso, lento o riesgoso cambiar de servicio. Puede surgir por APIs propietarias, formatos cerrados, datos difíciles de exportar, conocimiento especializado, contratos o costos de transferencia. No toda dependencia es inaceptable: aceptar lock-in puede ser razonable si el beneficio supera el costo y se entiende la salida.

Reducir lock-in no significa evitar toda funcionalidad específica. Significa decidir conscientemente dónde se acepta, aislarla mediante contratos, conservar datos exportables, documentar alternativas y probar una ruta de migración cuando el riesgo lo justifique. La portabilidad total también tiene un costo: puede impedir aprovechar capacidades que resuelven un problema real.

## Método de evaluación

Usa esta secuencia para comparar alternativas:

1. **Define el escenario.** Describe carga, usuarios, datos, objetivo de calidad y horizonte temporal.
2. **Enumera drivers y restricciones.** Separa lo imprescindible de lo deseable y registra supuestos.
3. **Construye alternativas reales.** Incluye una opción simple, una intermedia y una de mayor capacidad o control.
4. **Calcula costo total.** Considera construcción, licencias, infraestructura, operación, soporte, migración, fallos y salida.
5. **Expone trade-offs.** Para cada opción, indica qué mejora, qué empeora, qué riesgo introduce y qué consecuencia se acepta.
6. **Evalúa reversibilidad.** Distingue decisiones de dos vías, que pueden cambiarse con bajo costo, de decisiones de una vía, cuyo cambio es difícil.
7. **Define revisión.** Establece métricas, fecha o evento que justificaría mantener, modificar o reemplazar la decisión.

No es necesario convertir la evaluación en una fórmula falsa de precisión. Una matriz cualitativa respaldada por cifras relevantes puede ser mejor que un puntaje que esconda supuestos. Lo importante es que otra persona pueda entender cómo se llegó a la recomendación.

## Cierre

La arquitectura es una disciplina de compromisos explícitos. Costo, rendimiento, confiabilidad, escalabilidad, flexibilidad y velocidad compiten por recursos y atención. La siguiente semana estos trade-offs se convertirán en decisiones comunicables mediante ADR, con drivers, alternativas, riesgos y consecuencias registradas de forma durable.