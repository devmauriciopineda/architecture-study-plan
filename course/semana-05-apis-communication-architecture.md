# Semana 5. APIs y arquitectura de comunicación

## Propósito

Aprender a elegir cómo se comunican los componentes de un sistema distribuido. La decisión no consiste en escoger una tecnología por popularidad, sino en definir quién necesita una respuesta inmediata, quién puede continuar después, cómo se aíslan los fallos y qué contrato permite evolucionar a los participantes sin perder interoperabilidad.

## La comunicación como decisión arquitectónica

Una **API** (Application Programming Interface) es un contrato mediante el cual un componente expone operaciones o información para que otro componente las utilice. El contrato puede describir solicitudes, respuestas, errores, seguridad y reglas de evolución. Una API no es necesariamente una URL: también puede ser una interfaz interna, un mensaje o un evento.

La **arquitectura de comunicación** define los patrones y límites que conectan capacidades del sistema. Una decisión inicial es distinguir entre **comunicación síncrona** y **comunicación asíncrona**.

En la **comunicación síncrona**, el solicitante espera una respuesta antes de continuar con esa operación. El modelo facilita consultar, validar o confirmar algo en el mismo flujo, pero expone al solicitante a la latencia y disponibilidad del receptor. Un timeout puede dejar incierto si el receptor procesó la solicitud.

En la **comunicación asíncrona**, el emisor entrega una solicitud o publica un hecho sin exigir que el receptor complete su trabajo antes de que el emisor continúe. El procesamiento puede ocurrir después y otro mecanismo comunica el resultado. Este modelo desacopla tiempos y puede absorber picos de carga, pero introduce estados intermedios, observabilidad más compleja y, con frecuencia, consistencia eventual.

La pregunta correcta no es si la comunicación asíncrona es “mejor”, sino qué garantía necesita cada interacción. Una consulta que decide si una operación puede continuar suele requerir una respuesta síncrona. La generación de un informe o el envío de un recordatorio puede ejecutarse después sin bloquear la acción principal. Una misma capacidad puede usar ambos estilos en operaciones distintas.

## REST

**REST** (Representational State Transfer) es un estilo arquitectónico para diseñar servicios alrededor de recursos identificables y de operaciones uniformes. Un recurso es una entidad o colección que el cliente puede consultar o modificar, como una inscripción o un certificado. En una API REST, los métodos HTTP y las representaciones transportan la intención y el estado visible del recurso.

REST favorece interfaces relativamente generales: el cliente trabaja con recursos y sus representaciones, no con una función privada del servidor. Sus ventajas suelen ser una semántica conocida, interoperabilidad amplia y facilidad para inspeccionar solicitudes y respuestas. Sus límites aparecen cuando la operación no encaja naturalmente como manipulación de un recurso, cuando se necesitan consultas muy específicas o cuando la representación exige demasiados intercambios.

Una API REST bien definida debe aclarar códigos de resultado, validaciones, errores, paginación, filtros, autenticación y reglas de modificación. La etiqueta REST por sí sola no garantiza una buena API. Un diseño puede usar HTTP y seguir teniendo recursos ambiguos, contratos inestables o respuestas que exponen detalles internos.

## RPC y gRPC

**RPC** (Remote Procedure Call, llamada a procedimiento remoto) presenta una operación remota con una forma parecida a invocar una función: el cliente solicita una acción con parámetros y recibe un resultado. Este modelo puede expresar con claridad acciones de negocio, especialmente cuando una operación no es una simple lectura o modificación de un recurso.

**gRPC** es un framework de RPC que normalmente usa contratos definidos con Protocol Buffers y HTTP/2 para transportar llamadas. Sus contratos tipados, generación de clientes y soporte para streaming pueden ser útiles en comunicaciones internas de alto volumen o entre equipos que controlan ambos extremos.

REST y RPC no representan una oposición entre “correcto” e “incorrecto”. REST suele facilitar integración abierta y comunicación orientada a recursos; RPC puede hacer explícita una operación y su esquema. RPC también puede crear un acoplamiento mental peligroso: una llamada remota no tiene las mismas garantías que una función local. Aunque el cliente vea `calcularResultado()`, debe tratar la operación como una interacción sujeta a latencia, timeouts, fallos parciales, compatibilidad e idempotencia.

## Mensajes, colas y Pub/Sub

Un **mensaje** es una unidad de datos enviada para solicitar trabajo o transportar información. Puede representar un comando, que pide una acción, o un hecho, que informa que algo ocurrió. El significado debe ser explícito: “emitir certificado” solicita trabajo; “curso completado” describe un hecho que otros pueden consumir.

Una **cola** es un mecanismo en el que los mensajes esperan ser procesados por consumidores. En el modelo habitual, un mensaje se entrega a un consumidor y se confirma después de procesarlo. La cola permite desacoplar la velocidad del productor y del consumidor, absorber picos y reintentar trabajo fallido. También exige decidir cuánto tiempo conservar mensajes, cómo tratar mensajes no procesables y cómo evitar que un consumidor lento acumule una espera indefinida.

**Pub/Sub** (Publish/Subscribe, publicación y suscripción) es un patrón donde un publicador emite un mensaje hacia un tema o canal y varios suscriptores reciben su propia entrega. A diferencia de una cola de consumidores competidores, Pub/Sub permite que varias capacidades reaccionen al mismo hecho sin que el productor conozca a cada consumidor. Esta flexibilidad reduce acoplamiento directo, pero puede dificultar descubrir quién depende de un evento y controlar el costo de sus efectos.

La entrega de mensajes normalmente debe asumir duplicados, reordenamiento o retraso salvo que el mecanismo ofrezca garantías explícitas y estas se utilicen correctamente. Por ello el consumidor debe ser idempotente, registrar el progreso y tolerar que el mismo hecho aparezca nuevamente. “Procesado” no debe significar simplemente “recibido”: la confirmación debe ocurrir cuando el efecto requerido quedó persistido o se decidió de manera segura qué hacer con el error.

## Eventos y Webhooks

Un **evento** es una representación de algo que ocurrió en el dominio o en el sistema, con el contexto necesario para que otro componente reaccione. Un evento debe ser pasado e inmutable en significado: `CursoCompletado` informa un hecho, no ordena al consumidor cómo implementarlo. El productor publica el evento sin asumir qué consumidores existirán, aunque sí debe conservar un contrato estable.

Un **webhook** es una notificación HTTP enviada desde un sistema hacia una URL registrada por otro sistema cuando ocurre un evento. Es una forma práctica de integración entre organizaciones o productos que no comparten el mismo mecanismo interno. El receptor puede estar temporalmente indisponible, por lo que el emisor necesita timeouts, reintentos y una forma de evitar duplicados. El receptor también debe autenticar el origen y validar el contenido; no debe confiar en que una solicitud externa es legítima solo porque llega a su endpoint.

Un webhook suele ser más simple que una plataforma completa de mensajería, pero ofrece menos control sobre retención, consulta histórica y distribución a muchos consumidores. La elección depende de la relación entre las partes y del nivel de entrega, auditoría y recuperación requerido.

## API gateways y control de acceso al tráfico

Un **API gateway** es un componente que recibe solicitudes externas y las dirige hacia servicios internos. Puede centralizar autenticación, autorización de entrada, terminación de TLS, observabilidad, transformación limitada y políticas de tráfico. También puede ocultar la topología interna y presentar una interfaz coherente a los clientes.

El gateway no debe convertirse en el lugar donde se acumula toda la lógica de negocio. Si decide reglas de cursos, evaluaciones o certificados, el límite de negocio queda duplicado y cada cambio exige coordinar componentes. Su responsabilidad es proteger y encaminar la frontera; las reglas deben permanecer en la capacidad propietaria.

**Rate limiting** (limitación de tasa) es la política que restringe cuántas solicitudes puede realizar un cliente, usuario o grupo durante un intervalo. Protege capacidad y reduce abuso, pero no sustituye la planificación de capacidad. La política debe definir qué identidad se limita, qué respuesta recibe quien supera el límite, si existe una cuota compartida y cómo se comporta el cliente ante un rechazo. También debe distinguir operaciones costosas de consultas ligeras.

Un gateway y el rate limiting son útiles en el borde, pero no eliminan la necesidad de límites internos. Un consumidor confiable también puede saturar a un proveedor por una campaña o por reintentos mal configurados. Las políticas de tráfico deben coordinarse con timeouts, backpressure y capacidad del receptor.

## Idempotencia y contratos

La **idempotencia** es la propiedad por la que repetir una solicitud produce el mismo efecto final que ejecutarla una sola vez. Es esencial cuando hay reintentos, porque una respuesta perdida no permite saber si el receptor ya actuó. Las consultas suelen ser idempotentes; los comandos que crean, envían o incrementan algo deben diseñarse con cuidado.

Una **clave de idempotencia** es un identificador único que acompaña una solicitud y permite al receptor reconocer repeticiones. El receptor debe asociar la clave con el resultado y rechazar o tratar consistentemente una repetición con parámetros incompatibles. Para eventos, un identificador de evento y una tabla o registro de eventos procesados cumplen una función semejante. La idempotencia no significa que no pueda haber errores: significa que repetir de forma segura no añade efectos indebidos.

Un **contrato de comunicación** es el acuerdo verificable sobre formato, semántica y comportamiento de una interacción. Incluye nombres y tipos de campos, obligatoriedad, unidades, estados, errores, autenticación, límites de tamaño, orden, duplicados, tiempos y compatibilidad. También debe indicar qué significa una confirmación y qué debe hacer cada parte cuando la otra no responde.

La evolución del contrato requiere compatibilidad. Agregar un campo opcional suele ser menos disruptivo que cambiar el significado de uno existente. El productor debe evitar eliminar o reinterpretar datos mientras existan consumidores antiguos. El consumidor debe ignorar campos desconocidos cuando sea seguro y validar lo que necesita. En contratos de eventos, conviene conservar el significado histórico: cambiar el evento para expresar otra cosa puede requerir un nuevo nombre.

## Marco de selección

Para elegir un mecanismo de comunicación, aplica estas preguntas en orden:

1. **¿Se necesita una respuesta para completar la operación actual?** Si sí, empieza evaluando comunicación síncrona. Si no, considera una cola o un evento.
2. **¿Quién conoce a quién?** Una relación directa y controlada puede usar REST o RPC. Muchos consumidores independientes favorecen Pub/Sub.
3. **¿Qué ocurre si el receptor está lento o caído?** Define timeout, reintentos, respuesta degradada, cola, retención y recuperación manual.
4. **¿La operación puede repetirse?** Si no es naturalmente idempotente, define una clave o un registro de deduplicación antes de permitir reintentos.
5. **¿Qué contrato y ritmo de cambio tienen las partes?** Considera versionado, consumidores externos, compatibilidad y propiedad del esquema.
6. **¿Qué volumen y distribución se esperan?** Evalúa tamaño de mensajes, frecuencia, picos, concurrencia y necesidad de limitar tráfico.
7. **¿Qué observabilidad se necesita?** Debe ser posible seguir una operación desde su solicitud hasta sus efectos, incluidos reintentos y errores.

La opción final debe expresar beneficios y costos. Por ejemplo, elegir una cola puede mejorar la resiliencia ante picos, pero obliga a aceptar procesamiento posterior y a informar estados intermedios. Elegir una llamada síncrona puede simplificar la experiencia de una validación, pero acopla su disponibilidad a la dependencia. Hacer explícito el compromiso evita que la arquitectura prometa garantías incompatibles.

## Cierre

Las APIs y los mecanismos de comunicación son contratos sobre tiempo, datos, errores y responsabilidad. REST, RPC, mensajes, colas, Pub/Sub y webhooks son herramientas para distintas relaciones, no sustitutos intercambiables. En la siguiente semana se estudiará la arquitectura de datos; allí habrá que decidir quién posee la información, cómo se replica y qué consistencia necesitan los contratos definidos aquí.