# Semana 8. Confiabilidad, disponibilidad y resiliencia

## Propósito

Aprender a diseñar sistemas que continúen prestando un servicio aceptable ante fallos y que puedan recuperarse cuando una interrupción sea inevitable. La confiabilidad no se obtiene prometiendo que nada fallará: se construye identificando fallos, limitando su impacto, detectándolos, degradando de forma controlada y recuperando el servicio con objetivos verificables.

## Disponibilidad, confiabilidad y resiliencia

La **disponibilidad** es la proporción de tiempo o de solicitudes durante la que un sistema presta el servicio acordado. No significa que todos los componentes estén activos ni que cada función responda perfectamente. Un sistema puede estar disponible para consultar información básica mientras una función secundaria está temporalmente deshabilitada.

La **confiabilidad** es la capacidad de funcionar correctamente durante un periodo y bajo condiciones definidas. Incluye ausencia de errores, integridad de resultados y cumplimiento del comportamiento esperado. Un sistema puede estar disponible porque responde rápidamente con datos incorrectos, pero no sería confiable. Disponibilidad y confiabilidad deben medirse según el servicio que recibe el usuario.

La **resiliencia** es la capacidad de absorber una perturbación, mantener o recuperar un nivel aceptable de servicio y volver a una condición normal o conocida. Incluye prevención, detección, respuesta y recuperación. La resiliencia no elimina el fallo; reduce su duración, alcance o impacto.

Estas propiedades se relacionan, pero no son intercambiables. Una copia redundante puede mejorar disponibilidad, pero si contiene datos corruptos no mejora confiabilidad. Una recuperación rápida puede elevar disponibilidad después de un incidente sin evitar que el mismo incidente se repita. El diseño debe declarar qué servicio se protege y qué degradación es aceptable.

## Tolerancia a fallos y redundancia

La **tolerancia a fallos** es la capacidad de continuar ejecutando una operación cuando una parte del sistema falla. Puede lograrse con alternativas, reintentos controlados, aislamiento, réplicas o procesamiento posterior. Tolerar un fallo no significa ocultar todos los errores: a veces la respuesta correcta es rechazar una operación para proteger la integridad.

La **redundancia** consiste en disponer de más de un recurso capaz de cumplir una función. Puede ser redundancia de procesos, nodos, redes, almacenamiento, fuentes de energía o copias de datos. La redundancia solo ayuda si los recursos no comparten el mismo fallo y si existe un mecanismo para detectar y utilizar la alternativa.

La redundancia activa distribuye trabajo entre varias instancias; la redundancia pasiva mantiene una alternativa preparada para asumirlo. Ambas tienen costos de sincronización, pruebas y operación. Una copia que nunca se restaura ni se prueba no es evidencia suficiente de recuperación. Añadir componentes duplicados también puede aumentar la complejidad y crear nuevos modos de fallo.

## Dominios de fallo

Un **dominio de fallo** es un conjunto de componentes que puede verse afectado por una misma causa. Una máquina, un rack, una red, una sala, una región o un proveedor pueden ser dominios distintos. Si todas las réplicas residen en el mismo dominio, un único incidente puede inutilizarlas juntas.

Identificar dominios de fallo permite decidir qué separación aporta valor. No siempre es necesario distribuir recursos entre regiones: primero hay que conocer las amenazas, el costo y el tiempo de recuperación requerido. La redundancia dentro de un dominio puede proteger contra una instancia defectuosa, pero no contra la pérdida del dominio completo.

## Health checks y detección

Un **health check** o comprobación de salud es una verificación que informa si un componente puede recibir trabajo o cumplir una función específica. Una comprobación superficial puede demostrar que un proceso está activo, pero no que pueda leer datos, acceder a una dependencia necesaria o responder dentro de un tiempo aceptable.

Conviene distinguir una comprobación de vida de una comprobación de preparación. La primera indica que el proceso sigue ejecutándose; la segunda indica que está listo para atender tráfico. Un health check demasiado estricto puede retirar instancias sanas durante una falla transitoria de una dependencia; uno demasiado débil puede enviar solicitudes a componentes incapaces de completar operaciones.

La detección debe producir una acción: retirar tráfico, alertar, iniciar recuperación o permitir degradación. Medir salud sin definir quién decide y qué sucede después solo genera señales sin resiliencia.

## Timeouts y reintentos

Un **timeout** es el límite que impide esperar indefinidamente una respuesta. Es una protección de recursos y una parte del contrato de una dependencia. Debe ser suficientemente amplio para el trabajo esperado, pero finito para evitar que una falla lenta consuma todas las conexiones.

Un **reintento** repite una operación que no produjo una respuesta utilizable. Ayuda ante fallos transitorios, pero puede multiplicar carga durante una saturación o duplicar efectos si la operación no es idempotente. Los reintentos deben tener un límite, una espera controlada y una condición clara para detenerse. El cliente no debe reintentar todas las clases de error de la misma manera.

Timeouts y reintentos forman una política conjunta. Si varias capas reintentan a la vez, el número real de solicitudes puede crecer exponencialmente. El diseño debe asignar una responsabilidad principal para reintentar, propagar un plazo restante y registrar cada intento. Después del límite, debe existir una respuesta explícita: fallar, aplazar, usar una copia o pedir intervención.

## Circuit breakers

Un **circuit breaker** o interruptor de circuito es un mecanismo que deja de enviar temporalmente solicitudes a una dependencia que está fallando. Normalmente tiene estados cerrado, abierto y semiabierto: permite tráfico normal, bloquea llamadas durante una pausa y prueba de forma limitada si la dependencia se recuperó.

El circuit breaker evita que una dependencia fallida consuma todos los recursos del llamador y permite fallar rápido. No repara el receptor ni reemplaza un timeout. Sus umbrales deben considerar errores, latencia y duración de la falla; si se abren demasiado pronto, pueden ocultar recuperaciones; si se abren demasiado tarde, el daño ya puede haberse propagado.

## Degradación controlada

La **degradación controlada** es la reducción intencional y conocida de una capacidad para conservar una parte más importante del servicio. Puede consistir en mostrar datos en caché, desactivar una recomendación, posponer una notificación o permitir una consulta con menos detalle.

Degradar no es devolver cualquier respuesta. El sistema debe distinguir funciones esenciales de secundarias, indicar estados incompletos cuando corresponda y evitar violar reglas de seguridad o integridad. No se debe usar una copia antigua para autorizar una operación crítica si esa decisión puede ser incorrecta.

La degradación debe diseñarse antes del incidente. Para cada dependencia conviene preguntar qué puede omitirse, qué puede aplazarse, qué puede servirse desde una copia y qué debe detenerse. La decisión debe ser observable para que el equipo sepa que está operando en modo degradado.

## Recuperación ante desastres

La **recuperación ante desastres** es el conjunto de estrategias y procedimientos para restaurar un servicio después de una pérdida importante de infraestructura, datos o capacidad operativa. Un desastre puede ser una falla de hardware, corrupción, error humano, incendio, pérdida de red o incidente de seguridad.

La recuperación incluye respaldos, copias fuera del dominio de fallo, procedimientos, responsables, acceso a herramientas y pruebas periódicas. Un respaldo protege datos, pero no garantiza que la aplicación pueda ejecutarse, que las credenciales estén disponibles o que el procedimiento sea suficientemente rápido. La recuperación debe considerar dependencias, orden de restauración y validación de integridad.

El **RTO** (Recovery Time Objective, objetivo de tiempo de recuperación) es el tiempo máximo aceptable para restaurar el servicio después de una interrupción. El **RPO** (Recovery Point Objective, objetivo de punto de recuperación) es la cantidad máxima de datos que se acepta perder, expresada como tiempo entre el último estado recuperable y el momento del incidente.

Un RTO de dos horas exige una estrategia distinta de un RTO de dos días. Un RPO pequeño requiere respaldos o replicación más frecuentes y puede elevar costo y complejidad. RTO y RPO son objetivos de negocio y operación, no propiedades automáticas de una tecnología. Deben validarse con ejercicios de restauración y medirse con resultados reales.

## SLI, SLO y SLA

Un **SLI** (Service Level Indicator, indicador de nivel de servicio) es una métrica que observa una dimensión concreta del servicio, como porcentaje de solicitudes exitosas, latencia o frescura de un reporte. Debe tener una definición precisa, una fuente de datos y una ventana de medición.

Un **SLO** (Service Level Objective, objetivo de nivel de servicio) es el valor objetivo de un SLI durante un periodo. Por ejemplo, puede establecer que al menos cierto porcentaje de consultas válidas sea exitoso o que la latencia del percentil alto permanezca bajo un límite. Un SLO orienta diseño y operación; no es útil si no puede observarse.

Un **SLA** (Service Level Agreement, acuerdo de nivel de servicio) es un compromiso formal entre proveedor y consumidor que puede incluir objetivos, exclusiones, responsabilidades y consecuencias. Un SLA puede incorporar SLO, pero no toda métrica interna necesita convertirse en obligación contractual.

Los tres términos forman una cadena: el SLI mide, el SLO define la meta y el SLA acuerda responsabilidades. Medir demasiadas cosas no sustituye seleccionar indicadores que representen la experiencia y el riesgo. También conviene reservar un margen de error: consumir todo el presupuesto permitido de fallos deja poco espacio para cambios o incidentes.

## Método de diseño

Aplica esta secuencia para diseñar resiliencia:

1. **Define el servicio.** Describe qué operación debe protegerse, para quién y qué significa una respuesta correcta.
2. **Clasifica fallos.** Enumera componentes, dominios de fallo, fallos rápidos, fallos lentos, corrupción y errores operativos.
3. **Establece prioridades.** Separa funciones esenciales, degradables y aplazables; identifica invariantes que nunca deben romperse.
4. **Diseña detección y contención.** Define health checks, timeouts, reintentos, circuit breakers y límites de propagación.
5. **Diseña recuperación.** Determina redundancia, respaldos, orden de restauración, RTO, RPO y responsables.
6. **Mide objetivos.** Selecciona SLI, fija SLO y aclara qué compromisos, si corresponde, formarán parte de un SLA.
7. **Prueba el escenario.** Ejecuta simulaciones o ejercicios controlados y registra el tiempo, la pérdida, la degradación y las acciones que no funcionaron.

## Cierre

La resiliencia combina diseño, operación y aprendizaje. Redundancia sin dominios de fallo, reintentos sin límites, respaldos sin restauración probada y métricas sin objetivos producen una sensación de protección, no una garantía. En la siguiente semana estas decisiones se relacionarán con cloud, on-premise e híbrido, donde el lugar y el modelo de operación cambian los dominios de fallo, los costos y las responsabilidades de recuperación.