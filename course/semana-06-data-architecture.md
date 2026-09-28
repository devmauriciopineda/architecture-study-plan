# Semana 6. Arquitectura de datos

## Propósito

Aprender a diseñar una arquitectura de datos a partir del significado de la información, sus patrones de uso y sus obligaciones de evolución. La elección del almacenamiento no debe comenzar por una tecnología: debe comenzar por saber quién es dueño de cada dato, qué operaciones necesita, qué consistencia exige y qué costo tiene conservarlo, moverlo o reconstruirlo.

## Los datos como parte de la arquitectura

La **arquitectura de datos** define cómo se representan, almacenan, consultan, protegen, transforman, replican y eliminan los datos de un sistema. No se limita al esquema de una base de datos. Incluye la propiedad de la información, los flujos entre capacidades, los patrones de carga, las garantías de consistencia, la evolución y las dependencias externas.

Un dato tiene significado dentro de un contexto. El estado actual de una inscripción, una respuesta histórica de una evaluación y un agregado mensual pueden referirse a la misma actividad, pero tienen necesidades distintas. Tratar todo como una única tabla o como una única copia de conveniencia puede simplificar el inicio y complicar la trazabilidad, el rendimiento o la evolución posterior.

## Relacional frente a NoSQL

Una base de datos **relacional** organiza la información en relaciones, normalmente tablas con filas y columnas, y permite expresar vínculos mediante claves y consultas declarativas. Sus restricciones de integridad, transacciones y lenguaje común la hacen adecuada cuando existen relaciones claras, reglas consistentes y consultas variadas sobre datos estructurados.

Una base de datos **NoSQL** agrupa familias de almacenes que no dependen principalmente del modelo relacional. Puede utilizar documentos, pares clave-valor, columnas anchas o grafos. Cada familia optimiza necesidades diferentes: un documento puede reunir la información que una lectura suele necesitar; un almacén clave-valor puede ofrecer acceso directo por identificador; un grafo puede representar relaciones navegables.

La comparación no es “relacional para sistemas pequeños y NoSQL para sistemas grandes”. Un modelo relacional puede escalar de forma suficiente y una base NoSQL puede ser una mala elección si el dominio necesita transacciones complejas o consultas ad hoc. La decisión debe partir de las relaciones, las operaciones, las garantías y la forma de crecimiento. También es posible combinar almacenes, pero cada copia introduce sincronización, operación y riesgo de divergencia.

## OLTP y cargas analíticas

Una carga **OLTP** (Online Transaction Processing, procesamiento transaccional en línea) está formada por operaciones frecuentes y relativamente pequeñas que crean o modifican el estado operativo. Ejemplos genéricos son registrar una inscripción, actualizar el progreso o guardar un intento. OLTP prioriza respuestas previsibles, integridad y transacciones breves.

Una carga **analítica** consulta y combina grandes volúmenes para descubrir tendencias, calcular indicadores o comparar periodos. Puede leer muchas filas, agrupar por dimensiones y ejecutar operaciones más costosas que una transacción individual. Un reporte de finalización por área y periodo es analítico aunque se ejecute desde una pantalla de la aplicación.

Usar el mismo almacén para ambos tipos puede ser apropiado al inicio si el volumen es moderado. A medida que crecen las consultas analíticas, pueden competir con las transacciones por CPU, memoria, conexiones o bloqueos. Separar una proyección analítica o un pipeline puede proteger el sistema operativo, pero añade retraso, duplicación y una nueva dependencia. La frontera correcta depende de la carga observada, no de una separación automática por nombres.

## Propiedad de los datos y ciclo de vida

La **propiedad de los datos** (data ownership) es la responsabilidad de una capacidad sobre el significado, las reglas de modificación, la calidad y la conservación de una información. El propietario decide qué valores son válidos y expone contratos para que otros los consulten. Ser consumidor no autoriza a modificar directamente el almacén del propietario.

La propiedad evita que varios módulos mantengan versiones contradictorias de la misma verdad. Puede haber copias de lectura, índices o proyecciones, pero deben reconocerse como derivadas. El sistema de empleados, por ejemplo, puede ser propietario de datos organizacionales; una aplicación que los usa puede conservar una representación local sin convertirse por eso en fuente maestra.

El **ciclo de vida de los datos** describe las etapas por las que pasa la información: creación, validación, uso activo, modificación, archivo, retención y eliminación. No todos los datos tienen la misma duración ni el mismo derecho de modificación. Un estado actual puede actualizarse; un resultado histórico puede requerir inmutabilidad; un documento puede tener una política de vencimiento.

Diseñar el ciclo de vida obliga a preguntar quién puede leer cada etapa, cuánto tiempo debe conservarse, cómo se corrige un error y qué evidencia debe mantenerse. La retención indefinida no es neutral: aumenta almacenamiento, exposición, respaldos y complejidad de búsqueda. Eliminar demasiado pronto puede impedir auditorías o reconstruir decisiones.

## Replicación, particionamiento y sharding

La **replicación** mantiene copias de los datos en más de un nodo o almacén. Puede mejorar disponibilidad, capacidad de lectura o recuperación, pero exige propagar cambios y decidir qué ocurre cuando una copia está retrasada o recibe una operación incompatible. Una réplica no es una fuente independiente de verdad salvo que el diseño defina cómo se coordinan las escrituras.

El **particionamiento** divide lógicamente un conjunto de datos en partes según una regla, como periodo, organización o tipo de registro. Permite limitar el trabajo de una consulta y organizar el ciclo de vida. Una partición puede existir dentro de un mismo sistema y no implica necesariamente varios servidores.

El **sharding** es la distribución de particiones entre nodos o almacenes distintos para repartir capacidad y carga. Puede permitir crecimiento horizontal, pero hace más complejas las consultas que cruzan particiones, las transacciones y los cambios de la regla de distribución. Una mala clave de shard concentra todo el tráfico en una sola partición, fenómeno conocido como hotspot.

Particionar o fragmentar no resuelve por sí solo un problema de rendimiento. Antes hay que conocer los patrones de consulta, la distribución de las claves, el tamaño de los datos y la necesidad de combinar resultados. La decisión también debe considerar respaldos, recuperación y operación, no solo la capacidad de escritura.

## Caching

Una **caché** es un almacenamiento de acceso rápido que conserva copias de datos para evitar recalcularlos o leerlos desde la fuente principal. Puede reducir latencia y carga, pero introduce una segunda representación que puede quedar desactualizada.

La caché requiere una política de expiración, invalidación y comportamiento ante fallos. Un **TTL** (Time To Live) fija cuánto tiempo puede vivir una entrada antes de considerarse vencida. Invalidar al cambiar la fuente puede reducir el retraso, pero es difícil coordinarlo cuando hay varias rutas de escritura. Servir datos antiguos puede ser aceptable para una portada pública y peligroso para una autorización o un límite de intentos.

El patrón **cache-aside** carga el dato desde la fuente cuando no está en caché y guarda el resultado para lecturas posteriores. Es sencillo y deja la fuente como autoridad, pero una escritura debe decidir cómo invalidar o actualizar la copia. La caché no debe convertirse accidentalmente en el único lugar donde existe un dato importante, salvo que el diseño asuma explícitamente esa responsabilidad y su recuperación.

## Consistencia, transacciones y consistencia eventual

La **consistencia** define qué versión y qué combinación de datos puede observar un lector después de las operaciones confirmadas. La consistencia fuerte busca que las lecturas respeten inmediatamente las escrituras confirmadas según el contrato. La consistencia eventual permite un intervalo en el que las copias difieren, con convergencia posterior si la propagación funciona.

Una **transacción** es una unidad de trabajo que se confirma completa o se deshace según reglas de atomicidad, consistencia, aislamiento y durabilidad. La atomicidad evita efectos parciales dentro de la transacción; la consistencia conserva invariantes; el aislamiento controla cómo interactúan operaciones concurrentes; la durabilidad conserva lo confirmado frente a fallos. Estas propiedades tienen límites y costos: una transacción más amplia puede bloquear más recursos o requerir coordinación.

La consistencia eventual es una decisión de negocio y no una excusa para ocultar retrasos. Si una aplicación muestra un reporte que tarda unos minutos en reflejar una finalización, debe comunicar o tolerar ese retraso. Si una regla impide superar un máximo de intentos, la comprobación y la modificación deben observar un estado suficientemente consistente para no aceptar dos operaciones incompatibles.

Una misma entidad puede necesitar garantías distintas según la operación. El estado operativo puede requerir una transacción fuerte, mientras que un agregado de lectura puede actualizarse de forma eventual. Separar esas vistas puede mejorar el rendimiento, pero exige identificar la fuente de verdad y el momento en que una proyección está lista para ser consultada.

## Pipelines de datos

Un **pipeline de datos** es una secuencia de pasos que extrae, valida, transforma, transporta y carga información desde una fuente hasta un destino. Puede ejecutarse en tiempo real, por lotes o mediante una combinación de ambos. Su diseño debe especificar frecuencia, orden, errores, duplicados, reanudación y trazabilidad.

Un pipeline puede producir una vista analítica a partir de hechos operativos sin permitir que el reporte modifique la fuente. Esto separa cargas y conserva ownership, pero introduce una ventana de retraso y una ruta adicional de recuperación. La calidad del pipeline debe medirse: cuántos registros llegaron, con qué retraso, cuántos fueron rechazados y si el resultado puede reproducirse.

La transformación debe conservar el significado de los datos y registrar su origen. Si un indicador cambia, debe ser posible explicar si cambió la fuente, la regla de cálculo o el periodo consultado. Un pipeline sin observabilidad puede generar números plausibles pero imposibles de auditar.

## Los datos como dependencia del sistema

Tratar los **datos como una dependencia del sistema** significa reconocer que una capacidad puede depender de la disponibilidad, calidad, latencia, versión y política de otro almacén o fuente. La dependencia puede ser local, como una base compartida, o externa, como un sistema corporativo.

Toda dependencia de datos necesita un contrato: propietario, campos y significados, frecuencia de actualización, consistencia, errores, límites, seguridad y ciclo de vida. También necesita una política ante indisponibilidad. Una copia local puede permitir continuar con datos conocidos, pero no debe presentarse como actual si la fuente cambió; bloquear una operación puede proteger una regla crítica, aunque reduzca disponibilidad.

El análisis debe distinguir qué datos son necesarios para decidir y cuáles solo enriquecen una respuesta. Si una dependencia secundaria falla, quizá sea posible continuar sin ese dato. Si la dependencia determina autorización o integridad, la operación puede tener que detenerse. Esta clasificación evita que toda falla externa tenga el mismo efecto.

## Método de diseño

Aplica esta secuencia antes de elegir un almacén:

1. **Inventaria los datos.** Nombra entidades, hechos, documentos, agregados y datos maestros; separa estado actual de historial.
2. **Asigna ownership.** Define propietario, consumidores, permisos de modificación y fuente de verdad para cada dato.
3. **Describe workloads.** Estima lecturas, escrituras, tamaño, frecuencia, picos, consultas y necesidad transaccional; clasifica OLTP o analítico.
4. **Define garantías.** Indica consistencia, disponibilidad, atomicidad, retraso aceptable y comportamiento ante una fuente indisponible.
5. **Diseña el ciclo de vida.** Especifica retención, archivo, corrección, eliminación, auditoría y recuperación.
6. **Evalúa copias y particiones.** Justifica replicación, caché, particionamiento, sharding o pipeline por una carga concreta y cuantificable.
7. **Prueba evolución.** Simula nuevos campos, cambios de volumen, migración de una fuente y reconstrucción de una proyección sin perder trazabilidad.

## Cierre

La arquitectura de datos conecta el significado del dominio con sus costos operativos. Relacional y NoSQL, OLTP y analítica, transacciones y consistencia eventual son alternativas condicionadas por workloads, ownership y consecuencias. En la siguiente semana estas decisiones se relacionarán con escalabilidad y rendimiento: allí habrá que medir qué capacidad crece, dónde aparece el cuello de botella y qué optimización conserva las garantías necesarias.