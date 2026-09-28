# Semana 7. Escalabilidad y rendimiento

## Propósito

Comprender cómo un sistema puede sostener más usuarios, datos o trabajo sin degradar de forma inaceptable su comportamiento. La escalabilidad no consiste en añadir máquinas por anticipado: requiere conocer la carga, localizar cuellos de botella y elegir la intervención más pequeña que resuelva el límite medido.

## Carga, rendimiento y escalabilidad

La **carga** es el trabajo que recibe un sistema: solicitudes por segundo, usuarios concurrentes, tamaño de documentos, mensajes pendientes o consultas ejecutadas. Sus características incluyen volumen, distribución, variabilidad, picos y proporción entre lecturas y escrituras.

El **rendimiento** describe cómo responde el sistema ante una carga concreta. Incluye el tiempo de respuesta, el uso de recursos y la cantidad de trabajo completado. La **escalabilidad** es la capacidad de conservar objetivos de rendimiento cuando aumenta la carga, mediante más recursos o una mejor distribución del trabajo. Un sistema rápido con poca carga no es necesariamente escalable.

La escalabilidad puede ser vertical u horizontal. El **escalado vertical** aumenta la capacidad de un nodo existente, por ejemplo añadiendo CPU, memoria o almacenamiento. Es simple de operar y puede conservar la consistencia de un proceso único, pero tiene un límite físico, puede requerir mantenimiento y concentra el riesgo en un nodo.

El **escalado horizontal** añade nodos para repartir la carga. Permite crecer por etapas y puede ofrecer redundancia, pero exige distribuir solicitudes, manejar estado compartido y coordinar datos. No es automáticamente mejor: si una base de datos, un bloqueo o una dependencia externa sigue siendo única, añadir servidores de aplicación no elimina ese límite.

La decisión debe relacionar costo, complejidad y patrón de crecimiento. Para una aplicación pequeña, vertical puede ser suficiente. Para una carga variable o una capacidad que debe crecer de forma independiente, horizontal puede ser más adecuado. En ambos casos se debe medir el resultado y comprobar que el nuevo recurso ataca el cuello de botella real.

## Arquitectura stateless y balanceo

Una arquitectura **stateless** (sin estado de sesión en el nodo) no depende de que una solicitud posterior llegue al mismo servidor que atendió la anterior. El estado necesario se guarda en un almacén compartido, se transporta en la solicitud o se mantiene en una dependencia explícita. Esto facilita añadir y retirar nodos, reiniciar instancias y distribuir tráfico.

Stateless no significa que el sistema no tenga estado. Significa que el estado de negocio no queda atrapado en la memoria local de una instancia. Una sesión, una carga temporal o una caché local todavía pueden existir, pero deben tener una política clara ante pérdida, duplicación o reubicación del nodo.

Un **load balancer** o balanceador de carga distribuye solicitudes entre varios destinos según una política. Puede usar turnos, conexiones, peso o señales de salud. Su función puede incluir terminar conexiones, detectar destinos no disponibles y retirar nodos defectuosos. El balanceador no corrige un servidor lento si todos los destinos comparten la misma base saturada, ni debe enviar tráfico a un nodo que responde técnicamente pero no puede completar trabajo útil.

## Caché y CDN

Una **caché** conserva temporalmente datos o resultados para reducir lecturas repetidas y latencia. Es útil cuando hay repetición, el dato cambia con menor frecuencia que la consulta y se tolera una política explícita de frescura. Una caché mal ubicada puede añadir invalidaciones, consumo de memoria y lecturas obsoletas sin resolver el límite principal.

Una **CDN** (Content Delivery Network, red de distribución de contenidos) almacena y sirve copias de contenido desde ubicaciones cercanas a los consumidores. Reduce distancia y carga sobre el origen para archivos estáticos, imágenes, hojas de estilo, videos externos o documentos públicos, según las reglas de seguridad. No sustituye una caché de datos operativos ni debe exponer información privada por una política de almacenamiento demasiado amplia.

La elección entre caché y CDN depende de qué se está almacenando y quién puede verlo. Ambos mecanismos reducen trabajo del origen, pero requieren TTL, invalidación, control de acceso y comportamiento ante contenido cambiado. La frescura no es una propiedad secundaria: para un curso publicado puede aceptarse un retraso corto; para una autorización o un resultado sensible, no.

## Connection pooling

**Connection pooling** o **agrupación de conexiones** mantiene un conjunto reutilizable de conexiones hacia una dependencia, como una base de datos. Abrir y cerrar una conexión para cada solicitud puede ser costoso; reutilizarla reduce negociación y latencia.

El pool tiene un tamaño limitado. Si es demasiado pequeño, las solicitudes esperan; si es demasiado grande, puede saturar al destino y aumentar la competencia por recursos. Un pool no crea capacidad adicional en la base de datos. Debe acompañarse de timeouts, límites de espera, liberación correcta y observación del uso, las conexiones activas y las solicitudes en cola.

## Backpressure y colas

**Backpressure** (contrapresión) es el mecanismo por el que un consumidor lento comunica al productor que debe reducir, pausar o limitar el ritmo de envío. Sin backpressure, una diferencia persistente entre producción y procesamiento acumula memoria, conexiones o mensajes hasta degradar todo el sistema.

Una **cola** almacena trabajo pendiente para desacoplar productores y consumidores. Puede absorber picos y permitir que un proceso posterior se ejecute a su propio ritmo. La cola no elimina el trabajo ni garantiza tiempo de respuesta: convierte una espera inmediata en una espera pendiente. Por eso hay que definir capacidad, edad máxima, prioridad, reintentos, descarte y una respuesta visible cuando el retraso sea relevante.

Backpressure puede expresarse rechazando solicitudes, limitando concurrencia, ralentizando productores o usando una cola con capacidad finita. La estrategia adecuada depende de si el trabajo puede perderse, repetirse, diferirse o presentarse parcialmente. Una cola infinita suele ocultar el problema hasta que el sistema queda sin recursos.

## Particionamiento y cuellos de botella

El **particionamiento** divide datos o trabajo según una clave, como usuario, periodo, región o tipo de operación. Permite que varios consumidores trabajen en paralelo y reduce el conjunto que cada consulta debe recorrer. La clave debe distribuir la carga de forma equilibrada y, al mismo tiempo, permitir las consultas importantes.

Un **cuello de botella** es el recurso o paso que limita el rendimiento total del sistema. Puede ser CPU, memoria, disco, red, conexiones, bloqueos, una consulta concreta, una dependencia externa o una cola de trabajo. El cuello puede moverse después de una optimización: acelerar la aplicación puede hacer visible la base de datos; aumentar consumidores puede saturar la red.

Optimizar sin medir produce cambios costosos y resultados inciertos. Es necesario observar utilización, saturación, errores, tiempo en cola y distribución del tiempo de respuesta. La media puede ocultar una cola larga: los percentiles altos muestran qué experimenta una parte de los usuarios durante picos o casos lentos.

## Throughput y latencia

El **throughput** o rendimiento de procesamiento es la cantidad de trabajo completado por unidad de tiempo, como solicitudes por segundo, mensajes por minuto o archivos procesados por hora. El throughput máximo depende de la capacidad del recurso limitante y de la eficiencia del trabajo.

La **latencia** es el tiempo que tarda una operación individual en producir una respuesta o completar un efecto. Un sistema puede tener alto throughput y latencia alta si procesa muchas operaciones en lotes. También puede ofrecer baja latencia para pocas solicitudes y saturarse al aumentar la concurrencia.

Mejorar una métrica puede empeorar otra. Aumentar el tamaño de un lote suele mejorar throughput, pero retrasa cada elemento. Paralelizar puede reducir latencia hasta que aparecen contención o colas. Los objetivos deben indicar qué operación se mide, bajo qué carga, en qué percentil y con qué tasa de errores.

## Capacity planning

La **planificación de capacidad** es el proceso de estimar recursos necesarios para una carga actual y futura, incluyendo un margen para variación y crecimiento. Relaciona demanda, capacidad por instancia, límites operativos, costo y tiempo necesario para ampliar recursos.

Una estimación útil parte de escenarios: tráfico normal, campaña previsible, pico inesperado y recuperación después de una interrupción. Debe incluir usuarios concurrentes, tasa de solicitudes, tamaños de respuesta, trabajos asíncronos, almacenamiento y dependencias. Las cifras deben validarse con pruebas, mediciones de producción o supuestos explícitos.

Planificar capacidad no es prometer que el sistema soportará cualquier carga. Es identificar el límite previsto, el indicador que lo anticipa y la acción disponible cuando se aproxima. Una política puede limitar tráfico, aplazar trabajos, añadir nodos, degradar una función secundaria o pedir intervención operativa.

## Método de análisis

Aplica esta secuencia antes de optimizar o escalar:

1. **Define el objetivo.** Especifica operación, carga, tasa de error, latencia objetivo y throughput esperado.
2. **Describe el perfil de carga.** Separa lectura y escritura, tráfico interactivo y trabajos de fondo, normalidad y picos.
3. **Mide el recorrido.** Observa cada dependencia, cola, pool de conexiones y recurso; identifica dónde espera el trabajo.
4. **Formula el cuello de botella.** Explica qué límite impide crecer y qué evidencia lo demuestra.
5. **Elige una intervención.** Considera mejorar una consulta, usar caché o CDN, particionar, ajustar pooling, escalar vertical u horizontalmente, aplicar backpressure o incorporar una cola.
6. **Prueba bajo carga representativa.** Compara percentiles, throughput, errores, saturación y costo antes y después.
7. **Define el siguiente límite.** Registra qué recurso quedará expuesto y cómo se observará cuando la carga aumente nuevamente.

## Cierre

La escalabilidad es una propiedad del sistema completo, no de una sola capa. Balancear servidores no ayuda si una dependencia sigue saturada; una caché no corrige una política de invalidación incorrecta; una cola no convierte trabajo lento en trabajo instantáneo. En la siguiente semana estas capacidades se relacionarán con confiabilidad y resiliencia: habrá que decidir cómo sostener el servicio cuando los recursos o componentes fallen, no solo cuando estén ocupados.