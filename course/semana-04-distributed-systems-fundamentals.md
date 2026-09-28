# Semana 4. Fundamentos de sistemas distribuidos

## Propósito

Comprender qué cambia cuando las partes de un sistema se comunican a través de una red. Al terminar la lectura podrás identificar límites remotos, distinguir fallos parciales de fallos totales, y razonar sobre latencia, consistencia, disponibilidad y coordinación antes de elegir mecanismos de comunicación.

## La red como límite arquitectónico

Un **sistema distribuido** es un sistema cuyas capacidades se ejecutan en procesos o nodos distintos y cooperan mediante comunicación. Un nodo puede ser un proceso, una máquina, un contenedor o un servicio; lo importante no es el nombre de la unidad, sino que la cooperación depende de una red.

En un monolito modular, dos módulos pueden tener límites conceptuales aunque compartan proceso y memoria. Cuando uno de ellos pasa a otro proceso, aparece la **red como límite**: la invocación deja de ser una transferencia local y se convierte en una operación que puede demorarse, perderse, duplicarse o encontrar un destino indisponible. Por eso un límite remoto no es solamente un cambio de despliegue. Cambia las hipótesis de diseño.

Una llamada local normalmente permite suponer que el resultado llegará pronto o que una excepción se observará directamente. Una llamada remota no permite suponer ninguna de las dos cosas. El emisor puede no saber si el receptor procesó la solicitud antes de que la conexión fallara. Esta incertidumbre obliga a definir qué significa repetir una operación, cuánto esperar y qué estado puede observar cada participante.

## Latencia y tiempo de espera

La **latencia** es el tiempo que transcurre entre el inicio de una operación y la recepción de su resultado. Incluye el tiempo de transmisión, el procesamiento del receptor, las colas y el retorno de la respuesta. No es constante: puede variar con la carga, la distancia, el tamaño del mensaje y el estado de los nodos.

La latencia se vuelve un problema arquitectónico cuando una operación compone varias llamadas remotas. Si una pantalla necesita cinco dependencias secuenciales, el tiempo percibido acumula sus demoras. Incluso llamadas paralelas pueden quedar limitadas por la más lenta. Además, una dependencia lenta puede consumir conexiones, hilos o memoria y producir una cascada de espera.

Un **timeout** (tiempo de espera máximo) es un límite explícito después del cual el cliente deja de esperar una respuesta. No demuestra que la operación haya fallado: solo demuestra que el cliente no obtuvo una respuesta dentro del plazo. Esta distinción es central. Tras un timeout, el servidor puede estar procesando, haber completado la operación o no haberla recibido.

Un timeout debe reflejar el propósito de la operación. Un usuario puede recibir una respuesta degradada para una consulta no esencial, mientras que una transacción crítica puede requerir una ruta de recuperación distinta. Esperar indefinidamente no conserva confiabilidad; desplaza el problema hacia el consumo de recursos y la experiencia del usuario.

## Fallos parciales y modos de fallo

Un **fallo parcial** ocurre cuando una parte del sistema falla o se vuelve inaccesible mientras otras partes continúan funcionando. Es una propiedad característica de los sistemas distribuidos: el catálogo puede responder mientras el proveedor de identidad no lo hace; el receptor puede guardar un mensaje mientras la respuesta se pierde; un nodo puede ver una réplica actualizada y otro una versión anterior.

Un **modo de fallo** es una forma concreta en la que una capacidad puede comportarse incorrectamente o dejar de estar disponible. Algunos modos frecuentes son:

- el nodo está caído o reiniciándose;
- la red interrumpe la comunicación en una dirección;
- la respuesta llega después del timeout;
- una solicitud o respuesta se pierde;
- una solicitud se procesa dos veces por una repetición;
- una dependencia devuelve datos incompletos, inválidos o desactualizados;
- las réplicas observan estados diferentes;
- un nodo funciona, pero está saturado y responde con lentitud.

Enumerar modos de fallo evita tratar “el servicio falló” como una única situación. Cada modo puede requerir una respuesta distinta: rechazar, reintentar, usar datos locales, poner una operación en espera o pedir intervención. El análisis debe considerar también el efecto sobre los datos y no solo el código de estado de una respuesta.

## Reintentos e idempotencia

Un **reintento** es la repetición controlada de una solicitud que no produjo una respuesta utilizable. Puede ayudar ante fallos transitorios, como una breve interrupción de red, pero también puede empeorar una saturación. Por ello debe limitarse en cantidad y tiempo, y normalmente debe introducir una espera creciente entre intentos. La política concreta depende de si el fallo es transitorio y de cuánto cuesta repetir la operación.

La **idempotencia** es la propiedad por la que ejecutar una operación una o varias veces produce el mismo efecto final que ejecutarla una sola vez. Consultar un registro suele ser idempotente. Crear un certificado o enviar una notificación no lo es necesariamente: repetirlos puede producir dos certificados o dos mensajes.

Una operación no idempotente puede volverse repetible si utiliza una clave de idempotencia o un identificador único de operación. El receptor registra ese identificador y devuelve el resultado ya producido cuando recibe la misma solicitud nuevamente. Esto no elimina todos los problemas: hay que definir cuánto tiempo se conserva la clave, qué ocurre si llegan datos distintos con el mismo identificador y cómo se recupera el estado de una operación interrumpida.

Timeouts y reintentos deben analizarse juntos. Si el cliente reintenta sin saber si el primer intento tuvo efecto, la idempotencia protege contra duplicados. Si no puede garantizarse esa propiedad, repetir puede ser más peligroso que informar una incertidumbre y dejar la resolución a un proceso explícito.

## Estado distribuido y replicación

El **estado distribuido** es la información necesaria para continuar una operación que está almacenada, observada o modificada por más de un proceso o nodo. Incluye, por ejemplo, el estado de una inscripción, el número de intentos consumidos o la emisión de una evidencia.

El estado distribuido es difícil porque no existe una memoria compartida perfecta ni una visión instantánea del sistema. Dos operaciones pueden llegar en distinto orden a nodos diferentes. Un nodo puede confirmar un cambio mientras otro todavía conserva el valor anterior. Coordinar cada modificación exige comunicación y, por tanto, introduce latencia y nuevos fallos.

La **replicación** consiste en mantener copias de un estado en varios nodos o almacenes. Sus objetivos pueden ser aumentar la disponibilidad, acercar los datos a los consumidores, mejorar la capacidad de lectura o conservar una copia de recuperación. Replicar no significa que las copias sean automáticamente equivalentes: hace falta definir cómo se propagan los cambios, cómo se resuelven conflictos y qué versión puede observar un lector.

La replicación síncrona espera confirmación de varias copias antes de completar una operación; puede ofrecer una visión más uniforme, pero eleva la latencia y depende de más nodos. La replicación asíncrona confirma antes de que todas las copias se actualicen; suele reducir la latencia y tolerar mejor ciertas interrupciones, pero permite que un lector observe información antigua. La elección debe partir del significado del dato y del daño aceptable, no de una preferencia general por “más disponibilidad” o “más consistencia”.

## Consistencia y disponibilidad

La **consistencia** describe qué valores puede observar un consumidor y qué relación existe entre las operaciones confirmadas. En un sistema fuertemente consistente, una lectura posterior a una escritura confirmada observa el nuevo valor según el contrato definido. En un sistema de **consistencia eventual**, las copias pueden diferir temporalmente, pero convergen si dejan de producirse cambios y la propagación funciona.

Consistencia eventual no significa datos aleatorios ni ausencia de reglas. Requiere aceptar lecturas desactualizadas, definir el retraso tolerable y determinar cómo se resuelven conflictos o se muestra un estado intermedio. Puede ser apropiada para un reporte agregado o una bandeja de notificaciones. Es más delicada para reglas como impedir un intento adicional después de alcanzar un límite.

La **disponibilidad**, en este contexto, es la capacidad de responder a las solicitudes dentro de un comportamiento aceptable cuando se consulta el sistema. No equivale a que cada respuesta contenga el dato más reciente. Un sistema puede seguir disponible devolviendo una copia ligeramente antigua o una respuesta degradada; también puede preservar consistencia rechazando una operación cuando no puede verificar el estado correcto.

Consistencia y disponibilidad no son sinónimos de calidad. Son propiedades que se valoran según el caso de uso. Una arquitectura debe declarar qué lecturas pueden ser antiguas, qué operaciones deben rechazarse y qué información puede omitirse temporalmente. Esa precisión convierte una preferencia abstracta en un contrato verificable.

## CAP y coordinación

El **teorema CAP** afirma que, ante una partición de red, un sistema distribuido no puede garantizar simultáneamente consistencia fuerte y disponibilidad para todas las operaciones. Una **partición** es una pérdida de comunicación que hace que componentes que siguen activos no puedan coordinarse entre sí.

CAP no significa que un sistema solo pueda tener dos propiedades en todo momento ni que “se elija consistencia o disponibilidad” sin contexto. La partición es el escenario decisivo. Cuando los nodos no pueden comunicarse, mantener una visión consistente puede exigir rechazar o bloquear solicitudes; mantener respuestas disponibles puede implicar aceptar estados que todavía no se han reconciliado. En condiciones normales, un sistema puede ofrecer consistencia y disponibilidad, pero debe tener una estrategia para la partición.

La **coordinación** es el intercambio de información que permite a varios componentes acordar un orden, un estado o una decisión. La coordinación fuerte puede proteger invariantes, pero aumenta mensajes, latencia y superficie de fallo. Conviene preguntar si la regla realmente necesita un acuerdo distribuido o si puede dividirse en una operación local y un proceso posterior. Esta pregunta suele producir diseños más simples que intentar que todos los nodos confirmen todo.

## Método de análisis

Antes de separar un módulo o introducir una dependencia remota, aplica esta secuencia:

1. **Identifica el estado y su propietario.** Define qué dato cambia, quién puede modificarlo y qué otros componentes solo lo consultan.
2. **Clasifica la operación.** Distingue consulta, comando, notificación o proceso prolongado; anota si debe ser idempotente.
3. **Define el contrato temporal.** Establece latencia esperada, timeout, comportamiento ante respuesta tardía y datos aceptablemente desactualizados.
4. **Enumera fallos parciales.** Pregunta qué ocurre si el receptor está caído, lento, aislado, duplicado o actualizado con otra versión.
5. **Elige la política de recuperación.** Decide entre reintento limitado, respuesta degradada, cola, rechazo explícito o intervención manual.
6. **Revisa las invariantes.** Determina qué reglas no pueden violarse, como no superar un límite de intentos o no emitir dos evidencias para la misma finalización.
7. **Mide antes de distribuir.** Compara el beneficio esperado de replicar o separar con el costo de latencia, observabilidad, operación y coordinación.

## Cierre

Distribuir componentes transforma llamadas simples en acuerdos sobre tiempo, fallos y estado. Los límites remotos deben justificarse por necesidades de escala, aislamiento, disponibilidad o evolución, no solo por una división conceptual. En la siguiente semana estos fundamentos se aplicarán a APIs y mecanismos de comunicación síncronos y asíncronos, donde las decisiones de contrato harán visibles estas mismas consecuencias.