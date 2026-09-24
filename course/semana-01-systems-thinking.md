# Semana 1. Systems Thinking: dejar de pensar en código

## Propósito del capítulo

La arquitectura de software comienza antes de elegir lenguajes, frameworks o bases de datos. Comienza cuando se aprende a observar una situación como un sistema: un conjunto de elementos relacionados que, dentro de ciertos límites, transforma entradas en resultados y produce efectos sobre su entorno.

Este capítulo introduce un vocabulario para describir sistemas de software con rigor. El objetivo no es dibujar diagramas por sí mismos, sino construir un modelo suficientemente útil para comprender el problema, identificar decisiones relevantes y anticipar consecuencias. Una buena descripción sistémica conecta personas, procesos, información, tecnología y restricciones.

## 1. Systems thinking aplicado al software

**Systems thinking**, o pensamiento sistémico, es una forma de analizar una realidad atendiendo a los elementos que la componen, las relaciones entre ellos, los flujos que los conectan y los efectos que producen a lo largo del tiempo. Se diferencia de un análisis puramente reduccionista porque no estudia cada parte como si fuera independiente: considera también las interacciones y el comportamiento del conjunto.

Aplicado al software, el pensamiento sistémico implica formular preguntas como estas:

- ¿Qué situación o capacidad debe modificar el sistema?
- ¿Dónde empieza y termina aquello que estamos construyendo?
- ¿Quiénes interactúan con el sistema y qué esperan de él?
- ¿Qué información entra, cómo se transforma y qué resultados salen?
- ¿Qué partes dependen de otras, y qué ocurre cuando una falla o cambia?
- ¿Qué reglas, límites técnicos, económicos o legales condicionan las opciones?
- ¿Qué efectos secundarios aparecen después de una decisión?

Pensar sistémicamente no significa modelar todo con el mismo nivel de detalle. Significa elegir el nivel de observación adecuado para la decisión que se quiere tomar. Para decidir la responsabilidad de un módulo puede bastar una vista de componentes; para comprender un retraso operativo quizá sea necesario observar actores, colas, aprobaciones y sistemas externos.

## 2. Sistema y componente

Un **sistema** es un conjunto organizado de elementos que interactúan para producir uno o más resultados dentro de un contexto. Un sistema puede contener personas, procesos, datos, dispositivos, servicios y reglas. En arquitectura de software, el sistema de interés suele incluir software y también elementos externos indispensables para que el resultado sea posible.

Un **componente** es una parte identificable de un sistema que tiene una responsabilidad, una interfaz o una función distinguible. Un componente puede ser un módulo, un servicio, una base de datos, una cola, un proceso manual o un sistema externo. La palabra no define por sí sola el tamaño: un componente puede contener otros componentes.

La diferencia entre sistema y componente es relativa al punto de vista. Un servicio puede ser un componente del producto completo y, al mismo tiempo, un sistema compuesto por procesos, almacenamiento y dependencias. Por eso conviene declarar siempre el nivel de análisis. Confundir una parte con el sistema completo suele ocultar actores, datos o restricciones que quedan fuera del código.

Una **interfaz** es el conjunto de operaciones, mensajes, formatos o reglas mediante el cual un componente se relaciona con otro. La interfaz describe cómo se puede interactuar con un componente, no necesariamente cómo está implementado internamente.

## 3. Límites del sistema

El **límite del sistema** es la frontera conceptual que separa aquello que se analiza o controla directamente de aquello que pertenece al entorno. El límite no es necesariamente una frontera de red ni coincide siempre con un repositorio o una unidad de despliegue.

Definir el límite exige distinguir tres categorías:

- **Dentro del sistema:** elementos cuyo comportamiento, datos o evolución forman parte directa del diseño que se está realizando.
- **Fuera del sistema:** elementos del entorno que no se controlan directamente, pero pueden influir en el resultado.
- **En la frontera:** interfaces, acuerdos, responsabilidades y mecanismos de coordinación entre ambos lados.

Los límites son decisiones de modelado. Un sistema puede incluir un proveedor externo como dependencia del contexto sin incluir su implementación. También puede tratar un proceso humano como parte del sistema operativo completo aunque no sea automatizado. Un límite demasiado estrecho produce una visión incompleta; uno demasiado amplio vuelve el modelo difícil de usar y diluye las responsabilidades.

Un límite bien definido responde qué controlamos, qué observamos y qué contrato necesitamos con el exterior. También hace visibles los riesgos: cambios en una API externa, indisponibilidad de un proveedor, datos que no se pueden modificar o decisiones que requieren intervención humana.

## 4. Actores y stakeholders

Un **actor** es una persona, organización, sistema o dispositivo que interactúa directamente con el sistema para iniciar una acción, recibir un resultado o intercambiar información. Un actor se describe por su papel en una interacción, no necesariamente por su identidad individual.

Un **stakeholder** o parte interesada es cualquier persona, grupo u organización que puede afectar al sistema, verse afectada por él o tener un interés legítimo en sus resultados. Los usuarios son stakeholders, pero no todos los stakeholders usan la interfaz. También pueden serlo quienes operan la plataforma, definen políticas, financian el producto, auditan su cumplimiento o soportan sus consecuencias.

La distinción importa porque un actor ayuda a describir interacciones, mientras que un stakeholder ayuda a descubrir intereses, expectativas y restricciones. Un operador puede no iniciar casos de uso de negocio, pero necesitar diagnósticos y alertas. Un área legal puede no usar el sistema diariamente, pero imponer requisitos de conservación, privacidad o trazabilidad.

Para cada actor o stakeholder conviene identificar:

1. Qué objetivo persigue.
2. Qué información aporta o necesita.
3. Qué decisiones puede tomar.
4. Qué riesgos le preocupan.
5. Qué resultado considera aceptable.

## 5. Inputs, outputs y flujos de información

Un **input** o entrada es cualquier dato, evento, señal, solicitud o recurso que ingresa al sistema o a uno de sus componentes. Una entrada puede provenir de un usuario, otro sistema, un archivo, un sensor, un reloj o una tarea programada.

Un **output** o salida es el resultado que el sistema produce para un actor, otro sistema o su propio almacenamiento. Puede ser una respuesta, una notificación, un registro, un cambio de estado, un informe o una decisión.

La transformación entre entradas y salidas no siempre es inmediata ni determinista. Puede incluir validación, enriquecimiento, persistencia, cálculo, autorización, espera y manejo de errores. Para describirla con precisión, hay que considerar el significado de los datos, su calidad, su frecuencia, su volumen y su propietario.

Un **flujo de información** es el movimiento de datos o significado entre actores, componentes o límites del sistema. El flujo puede ser síncrono, cuando quien envía espera una respuesta dentro de la misma interacción, o asíncrono, cuando el emisor entrega el trabajo y el procesamiento o la respuesta ocurren después.

Describir un flujo requiere responder al menos:

- quién origina la información;
- qué representa y en qué formato viaja;
- quién la valida y quién la transforma;
- dónde se conserva;
- quién consume el resultado;
- qué sucede si está incompleta, duplicada, atrasada o no disponible.

Un flujo de información no equivale simplemente a una llamada entre funciones. Puede cruzar procesos, equipos, organizaciones y momentos distintos. El significado y la responsabilidad sobre los datos deben mantenerse claros durante todo el recorrido.

## 6. Dependencias

Una **dependencia** existe cuando un elemento necesita otro para completar una operación, mantener una propiedad o evolucionar correctamente. La dependencia puede ser de ejecución, de datos, de conocimiento, de infraestructura, de organización o de tiempo.

Ejemplos de dependencias son una aplicación que necesita una base de datos para persistir información, un proceso que requiere datos maestros de otro sistema, un servicio que depende de la disponibilidad de una API o un equipo que necesita el acuerdo de otro equipo para cambiar un contrato.

Toda dependencia introduce una relación de impacto: un cambio, retraso, error o indisponibilidad en el proveedor puede afectar al consumidor. Por eso una descripción arquitectónica debe registrar qué se necesita, quién es responsable, qué contrato existe y qué comportamiento se espera ante fallos.

La **dirección de dependencia** indica quién conoce o necesita a quién. Mantener esta dirección explícita ayuda a evitar ciclos, reducir acoplamiento y localizar los puntos donde una decisión se propaga. Un componente puede depender de una interfaz estable sin depender de una implementación concreta; esa diferencia es importante para preservar opciones de evolución.

## 7. Feedback loops

Un **feedback loop**, o bucle de retroalimentación, es una cadena en la que una salida del sistema vuelve a influir en sus entradas, decisiones o condiciones futuras. Los bucles explican comportamientos que no se comprenden observando una única operación aislada.

Un **bucle reforzador** amplifica una tendencia. Por ejemplo, más usuarios pueden generar más datos, esos datos pueden mejorar un servicio y el servicio mejorado puede atraer más usuarios. El crecimiento se acelera, pero también pueden amplificarse errores, costos o sesgos.

Un **bucle balanceador** reduce o corrige una desviación respecto de un objetivo. Un mecanismo de control de capacidad puede detectar saturación, limitar nuevas solicitudes y permitir que el sistema vuelva a un nivel operativo aceptable.

En software, los bucles pueden aparecer mediante reintentos, notificaciones, métricas, recomendaciones, procesos de aprobación, colas de trabajo o decisiones operativas. Un reintento que provoca más carga sobre un servicio degradado es un ejemplo de feedback potencialmente peligroso. Reconocer el bucle permite definir límites, señales de control y condiciones de salida.

## 8. Complejidad

La **complejidad** es la dificultad de comprender, predecir, cambiar u operar un sistema debido a la cantidad de elementos, relaciones, estados, reglas y dependencias que contiene. No es sinónimo de tamaño: un sistema pequeño con muchas interacciones puede ser más complejo que uno grande y regular.

La **complejidad esencial** proviene del problema que se debe resolver: reglas de negocio, actores con objetivos distintos, restricciones legales o necesidad de coordinar estados. La **complejidad accidental** proviene de las decisiones de implementación, herramientas, duplicación, acoplamiento innecesario o procesos difíciles de operar.

La arquitectura no elimina toda complejidad. La hace visible, la ubica y evita que se multiplique sin control. Para gestionarla se usan límites claros, vocabulario compartido, responsabilidades cohesionadas, contratos explícitos, automatización, observabilidad y decisiones proporcionales al riesgo.

También existe **complejidad dinámica**: la que aparece cuando el sistema cambia con el tiempo, cuando hay concurrencia, fallos parciales, eventos atrasados o estados intermedios. Un modelo estático puede ocultarla, por lo que conviene analizar escenarios y transiciones, no solo componentes.

## 9. Constraints

Una **constraint** o restricción es una condición que limita las soluciones admisibles. Puede ser técnica, de negocio, regulatoria, organizacional, temporal, presupuestaria o derivada de un sistema existente.

Una restricción no es lo mismo que una preferencia. "Debe cumplir una norma de privacidad" limita obligatoriamente el espacio de diseño; "preferimos determinada tecnología" orienta la decisión, pero puede admitir excepciones. Declarar la diferencia evita tratar decisiones reversibles como hechos inevitables.

Las restricciones pueden ser:

- **Duras:** si se incumplen, la solución no es aceptable.
- **Blandas:** expresan una preferencia o prioridad negociable.
- **Explícitas:** están documentadas o impuestas formalmente.
- **Implícitas:** se deducen del contexto, pero todavía deben validarse.

Una restricción útil se formula con su origen y su consecuencia. Por ejemplo: una política de retención puede exigir conservar ciertos registros durante un periodo; eso afecta el almacenamiento, el acceso y los procesos de eliminación. La arquitectura debe hacer visibles los compromisos que cada restricción introduce.

## 10. Requisitos funcionales y no funcionales

Un **requisito** es una condición o capacidad que el sistema debe cumplir para satisfacer una necesidad identificada. Los requisitos conectan objetivos del contexto con comportamientos verificables del sistema.

Un **requisito funcional** describe qué debe hacer el sistema: una capacidad, una regla, una respuesta o una transformación observable. Debe poder expresarse mediante un comportamiento comprobable, con actores, condiciones y resultado definidos.

Un **requisito no funcional** describe una propiedad, nivel de servicio o condición de operación del sistema, en lugar de una función de negocio concreta. Incluye atributos como seguridad, rendimiento, disponibilidad, mantenibilidad, accesibilidad, auditabilidad y privacidad.

La distinción es útil, pero no absoluta. Una misma capacidad puede tener requisitos de ambos tipos. Por ejemplo, registrar una operación es funcional; conservar el registro de manera íntegra, consultable y durante un periodo determinado añade requisitos no funcionales y de cumplimiento.

Los requisitos no funcionales deben ser medibles siempre que sea posible. "El sistema debe ser rápido" es ambiguo; "el 95 % de las consultas debe responder en menos de 300 ms bajo una carga definida" permite evaluar una solución. La métrica, el contexto de carga, el umbral y el método de verificación forman parte del requisito.

## 11. Método práctico de análisis

Un análisis sistémico inicial puede seguir esta secuencia:

1. **Definir el propósito:** describir qué resultado debe producir el sistema y para quién.
2. **Fijar el límite:** separar control directo, entorno y fronteras de integración.
3. **Identificar actores y stakeholders:** registrar objetivos, intereses y responsabilidades.
4. **Mapear entradas y salidas:** describir los flujos de información y sus transformaciones.
5. **Localizar dependencias:** anotar contratos, propietarios, puntos de fallo y cambios posibles.
6. **Buscar feedback:** revisar qué salidas modifican comportamientos futuros o aumentan la carga.
7. **Enumerar restricciones:** distinguir hechos, obligaciones, preferencias y supuestos.
8. **Separar requisitos:** formular capacidades funcionales y propiedades no funcionales verificables.
9. **Revisar escenarios:** comprobar qué ocurre durante operación normal, cambios y fallos.

El resultado no es un diseño definitivo. Es un modelo de trabajo que permite formular mejores preguntas y tomar decisiones con menos puntos ciegos. Debe revisarse cuando aparecen nuevos datos, cambia el contexto o una suposición deja de ser válida.

## Cierre

Pensar como arquitecto empieza por ampliar la unidad de observación. Una función puede ser correcta y, aun así, contribuir a un sistema confuso, frágil o imposible de operar. El pensamiento sistémico obliga a conectar comportamiento, contexto, personas, información, dependencias y restricciones.

La pregunta central de esta semana no es "¿qué código debo escribir?", sino: **¿qué sistema estoy intentando modificar, dónde están sus límites y qué relaciones determinan su comportamiento?** Las respuestas proporcionan la base para las siguientes semanas: convertir necesidades y restricciones en decisiones arquitectónicas explícitas.
