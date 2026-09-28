# Semana 12. Evolución arquitectónica

## Propósito

Comprender la arquitectura como una capacidad que cambia junto con el negocio, la tecnología, la carga y las restricciones. Al terminar la lectura podrás planificar cambios incrementales, proteger la compatibilidad, reconocer deuda técnica y usar criterios medibles para saber si una arquitectura sigue cumpliendo su propósito.

## Evolutionary architecture

Una **arquitectura evolutiva** es una arquitectura diseñada para cambiar de forma controlada a medida que aparecen nuevos requisitos, datos, cargas o restricciones. No intenta predecir todo el futuro ni justificar la ausencia de diseño. Define límites, contratos y mecanismos de aprendizaje que permiten modificar el sistema sin perder sus propiedades importantes.

La evolución puede afectar comportamiento, datos, despliegue, comunicación o ownership. Un nuevo canal de acceso puede exigir cambios de identidad; un aumento de reportes puede exigir separar workloads; una regulación puede modificar retención. La arquitectura debe distinguir qué partes son estables, qué partes pueden variar y qué evidencia indica que un límite dejó de ser adecuado.

Evolucionar no es reescribir por preferencia tecnológica. Una reescritura descarta comportamiento, datos y conocimiento acumulado, y vuelve a introducir riesgos ya resueltos. El cambio incremental permite aprender en producción o en entornos controlados, limitar el radio de fallo y conservar una ruta de retorno.

## Architecture runway

El **architecture runway** es el conjunto de capacidades arquitectónicas preparadas para soportar las próximas necesidades conocidas del producto. Puede incluir contratos, automatización, observabilidad, límites modulares, capacidad de datos, pruebas de compatibilidad o mecanismos de despliegue.

Un runway no es una plataforma construida para cualquier futuro imaginable. Es una inversión acotada que reduce el costo de una evolución probable. Si el producto necesitará nuevos consumidores, puede ser útil estabilizar un contrato; si se prevén cargas variables, puede ser útil medir y aislar un workload. Si no existe una necesidad razonablemente cercana, el runway puede convertirse en infraestructura sin uso.

El runway debe tener una dirección y un límite. Se revisa cuando cambian la estrategia, la carga o el conocimiento del dominio. Una arquitectura demasiado adelantada consume tiempo que podría invertirse en validar el producto; una arquitectura demasiado atrasada obliga a cambios urgentes y arriesgados.

## Migración incremental

Una **migración incremental** mueve una capacidad, un dato o una responsabilidad en pasos pequeños y verificables, manteniendo el servicio durante la transición. Cada paso debe tener un estado conocido, una forma de observar resultados y una estrategia para corregir o volver atrás.

Una migración suele separar estas preocupaciones: preparar el destino, copiar o traducir datos, introducir compatibilidad, mover tráfico, verificar resultados y retirar el origen. Durante un periodo pueden existir dos modelos o dos rutas, pero esa duplicación debe ser temporal y tener una condición de finalización. Sin un plan de salida, la migración crea dos sistemas permanentes.

Las migraciones de datos requieren especial cuidado. Copiar datos no resuelve ownership, cambios concurrentes, historiales, referencias, permisos ni validación. Puede ser necesario hacer una carga inicial, capturar cambios posteriores, comparar resultados y cambiar el propietario solo cuando la nueva fuente sea confiable.

## Strangler pattern

El **Strangler pattern** o patrón estrangulador reemplaza gradualmente capacidades de un sistema existente mediante una fachada o punto de entrada que dirige cada función al componente antiguo o al nuevo. La nueva capacidad toma responsabilidad por partes delimitadas hasta que el sistema original puede retirarse.

El patrón reduce el riesgo de una sustitución completa porque limita el cambio a un flujo o contexto. Requiere fronteras claras, enrutamiento, contratos, observabilidad y una estrategia para datos compartidos. Si el nuevo componente depende de leer y escribir directamente el modelo interno del antiguo, la separación es aparente y el retiro se vuelve difícil.

El patrón no siempre es conveniente. Una capacidad muy acoplada, sin límites identificables o con transacciones inseparables puede requerir primero una refactorización interna. También hay que vigilar que la fachada no acumule reglas de negocio y se convierta en un nuevo monolito difícil de evolucionar.

## De monolito a monolito modular

Un **monolito** es una aplicación desplegada como una unidad operativa, aunque pueda contener múltiples responsabilidades. Un **monolito modular** conserva esa unidad de despliegue, pero protege módulos con responsabilidades, datos y dependencias explícitos. La transición de monolito a monolito modular suele ser un paso evolutivo de bajo riesgo relativo.

El cambio puede comenzar identificando ownership, separando vocabulario, restringiendo accesos directos, definiendo contratos internos y moviendo reglas al módulo que las posee. No es suficiente crear carpetas o nombres de módulos: los límites deben observarse en dependencias, pruebas y cambios reales.

Un monolito modular puede mejorar comprensión, pruebas y futuras extracciones sin introducir llamadas de red entre capacidades. Su límite es que comparte proceso, despliegue y parte del radio de fallo. Eso puede ser aceptable cuando la carga, el equipo y la operación no justifican distribución.

## De monolito modular a servicios

La transición de un **monolito modular a servicios** extrae un módulo a un proceso desplegable y operable por separado. Al cruzar la red aparecen latencia, fallos parciales, contratos versionados, observabilidad distribuida, consistencia eventual y operación adicional.

Extraer un módulo tiene sentido cuando existe una razón demostrable: un ritmo de escalado distinto, un dominio de fallo que debe aislarse, un ciclo de despliegue independiente, una tecnología incompatible o un equipo que puede operar la capacidad. La existencia de un límite conceptual es necesaria, pero no suficiente.

La extracción debe comenzar por un contrato estable y una propiedad clara de datos. Se puede dirigir tráfico al nuevo servicio de forma gradual, observar latencia y errores, y conservar una ruta de retorno mientras el contrato se valida. Si el servicio necesita transacciones constantes con el resto del monolito o consultas directas a sus tablas, todavía no existe una frontera operable.

## Estrategias de migración y compatibilidad

Una **estrategia de migración** es el plan de orden, técnica y controles con los que una parte del sistema pasa de un estado a otro. Puede ser big bang, cuando todo cambia en una sola ventana; paralela, cuando ambos caminos conviven; por fases, cuando se mueve una población o capacidad a la vez; o incremental, cuando cada paso reduce el alcance del legado.

Big bang puede ser sencillo de explicar, pero concentra riesgo, coordinación y recuperación en un momento. Una migración paralela permite comparar, pero duplica costos y puede crear divergencia. Una migración por fases reduce el radio de fallo, aunque exige segmentación, métricas y soporte para estados mixtos. La elección depende de reversibilidad, riesgo, volumen y capacidad operativa.

La **compatibilidad** es la capacidad de versiones, componentes o sistemas diferentes para cooperar durante una transición. La compatibilidad hacia atrás permite que un consumidor antiguo funcione con un productor nuevo; la compatibilidad hacia adelante permite que un consumidor nuevo tolere respuestas o datos anteriores. Los contratos deben definir campos, estados, errores, versiones, duplicados y reglas de retiro.

La compatibilidad no se limita a APIs. Incluye esquemas de datos, eventos, formatos de archivos, permisos, procesos operativos y comportamiento de usuario. Agregar un campo opcional suele ser más compatible que cambiar el significado de uno existente. Las migraciones deben evitar cambiar productor y consumidor simultáneamente sin una etapa intermedia cuando sea posible.

## Deuda técnica y evolución bajo requisitos cambiantes

La **deuda técnica** es una obligación futura causada por decisiones que dejan limitaciones, excepciones o trabajo pendiente. Durante una evolución puede crecer cuando se agregan adaptadores temporales, duplicación de datos o rutas de compatibilidad. El hecho de que una solución sea temporal no evita la deuda; debe tener un propietario, una condición de retiro y un costo estimado.

Los requisitos cambiantes no justifican reaccionar con parches aislados. Cuando cambia un requisito, hay que distinguir si cambia una regla de negocio, un workload, un contrato, un objetivo de calidad o una restricción. Después se evalúa qué decisiones y principios quedan afectados. Esta práctica evita rediseñar todo por un cambio local o ignorar un cambio que rompe una garantía importante.

Una evolución saludable acepta que no todo debe optimizarse al mismo tiempo. Se puede conservar una arquitectura simple mientras se recopilan métricas, siempre que se protejan los límites que serían costosos de reconstruir. La deuda se administra con visibilidad y prioridades, no con la expectativa de eliminarla por completo.

## Architecture fitness

Una **architecture fitness function** o función de aptitud arquitectónica es una verificación automatizada o repetible que evalúa si una propiedad arquitectónica se mantiene. Puede comprobar que no existan dependencias entre módulos, que un contrato siga siendo compatible, que la latencia permanezca bajo un objetivo o que los datos sensibles no crucen un límite prohibido.

Las fitness functions convierten principios abstractos en señales observables. Deben medir una propiedad relevante y evitar convertirse en reglas sin propósito. Una prueba de dependencias puede proteger modularidad; una prueba de contrato puede permitir evolución de consumidores; una métrica de error puede detectar que una extracción empeoró el servicio.

Una función de aptitud no reemplaza el juicio arquitectónico. Puede pasar mientras el sistema viola una necesidad no medida, o fallar por una regla que dejó de ser válida. Debe tener un propietario, una respuesta ante incumplimiento y una revisión cuando cambien los objetivos.

## Método de evolución

Aplica esta secuencia antes de cambiar una arquitectura:

1. **Describe el estado actual.** Identifica ownership, contratos, dependencias, datos, workloads, deuda y propiedades que deben conservarse.
2. **Define el cambio y su motivo.** Relaciónalo con un requisito, una métrica, un riesgo o una restricción; evita evolucionar solo por moda.
3. **Diseña estados intermedios.** Define compatibilidad, coexistencia, migración de datos, observabilidad y ruta de retorno.
4. **Elige el alcance.** Prefiere un módulo, población o flujo acotado antes de cambiar todo el sistema.
5. **Protege las propiedades.** Añade pruebas, fitness functions y métricas para detectar regresiones.
6. **Mueve ownership gradualmente.** No retires el origen hasta verificar datos, consumidores, operación y recuperación.
7. **Retira lo temporal.** Elimina rutas antiguas, adaptadores y duplicación cuando se cumplan las condiciones de finalización.
8. **Actualiza las decisiones.** Reemplaza ADRs, registra consecuencias nuevas y ajusta el runway a lo aprendido.

## Cierre

Una arquitectura evolutiva no predice el futuro: prepara cambios verificables y limita el costo de equivocarse. El monolito modular puede ser una etapa válida antes de extraer servicios; el patrón estrangulador, la compatibilidad y las migraciones por fases reducen el riesgo; las fitness functions hacen visibles las propiedades que deben sobrevivir. Con esta semana se cierra el recorrido desde systems thinking hasta decisiones, operación y evolución arquitectónica: la arquitectura queda como una práctica continua de aprendizaje y ajuste.