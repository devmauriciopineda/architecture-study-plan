# Semana 3. Software Architecture & Modularity: límites, responsabilidades y dependencias

## Propósito del capítulo

La arquitectura de software define la forma significativa de un sistema: sus partes principales, sus responsabilidades, las relaciones entre ellas y las decisiones que condicionan su evolución. No es un diagrama decorativo ni una lista de tecnologías. Es un conjunto de decisiones estructurales que permite satisfacer requisitos y restricciones con un nivel de complejidad aceptable.

Este capítulo presenta la diferencia entre arquitectura y diseño, explica estrategias comunes de organización y desarrolla los principios que permiten evaluar una modularidad saludable. El objetivo es comprender qué límites conviene establecer, cómo deben dirigirse las dependencias y qué trade-offs aparecen al elegir entre un monolito modular y varios servicios.

## 1. Architecture vs. design

La **software architecture**, o arquitectura de software, es la organización fundamental de un sistema: sus elementos principales, las relaciones entre ellos, sus responsabilidades y las decisiones que afectan propiedades como seguridad, rendimiento, disponibilidad, mantenibilidad y evolución.

El **design**, o diseño detallado, es la definición de estructuras y comportamientos más específicos para implementar una responsabilidad. Incluye modelos de datos, algoritmos, interfaces concretas, validaciones, clases, funciones y decisiones locales.

La frontera entre ambos no es una línea fija. Una decisión es arquitectónica cuando su impacto atraviesa muchas partes del sistema, limita alternativas futuras, afecta atributos de calidad relevantes o resulta costosa de cambiar. Una decisión suele ser de diseño cuando se puede modificar dentro de un límite bien establecido sin alterar el resto del sistema.

La arquitectura responde preguntas como:

- ¿Qué partes conceptuales existen?
- ¿Qué responsabilidad pertenece a cada parte?
- ¿Qué dependencias se permiten?
- ¿Qué información cruza cada límite?
- ¿Qué decisiones deben mantenerse estables?
- ¿Cómo se satisfacen los requisitos no funcionales importantes?

El diseño responde cómo una parte concreta realiza su responsabilidad. La distinción evita dos errores opuestos: intentar resolver toda la arquitectura con detalles prematuros o tratar decisiones estructurales como si fueran simples elecciones locales.

Una **architecture decision**, o decisión arquitectónica, es una elección estructural con razones, alternativas y consecuencias explícitas. Documentarla ayuda a evitar que una preferencia accidental se convierta en una regla invisible.

## 2. Modular architecture

Una **modular architecture**, o arquitectura modular, organiza el sistema en módulos con responsabilidades definidas, interfaces claras y dependencias controladas. Un **módulo** es una unidad de organización que agrupa código, datos o reglas relacionados y oculta sus detalles internos detrás de un límite.

La modularidad no consiste simplemente en tener muchos directorios o clases pequeñas. Existe cuando un cambio relacionado con una responsabilidad puede realizarse principalmente dentro del módulo que la posee y cuando otros módulos dependen de contratos estables, no de detalles internos.

Un módulo saludable tiene:

- una responsabilidad comprensible;
- un límite explícito;
- una interfaz suficiente y no excesiva;
- datos y reglas que pertenecen coherentemente a esa responsabilidad;
- dependencias justificadas y dirigidas;
- pruebas que permitan verificarlo sin conocer todos sus detalles.

La modularidad es un medio para controlar el cambio. No elimina la complejidad del dominio; la localiza y evita que cada modificación obligue a comprender todo el sistema. También mejora la posibilidad de probar, sustituir, desplegar o asignar responsabilidades a equipos, aunque esas ventajas no aparecen automáticamente.

## 3. Coupling

El **coupling**, o acoplamiento, es el grado de dependencia entre módulos o componentes. Cuanto más conocimiento necesite una parte sobre otra, más acopladas están.

El acoplamiento puede aparecer de varias formas:

- **Acoplamiento de contenido:** un módulo modifica directamente los datos o la implementación interna de otro.
- **Acoplamiento de interfaz:** un consumidor depende de un contrato, lo cual es normal, aunque una interfaz inestable o demasiado amplia eleva el impacto del cambio.
- **Acoplamiento de datos:** varios módulos comparten estructuras, tablas o significados que deben cambiar juntos.
- **Acoplamiento temporal:** una operación necesita que otra ocurra antes o simultáneamente.
- **Acoplamiento de control:** un módulo debe conocer detalles del flujo interno de otro para indicarle cómo comportarse.
- **Acoplamiento de despliegue:** dos partes deben publicarse, escalarse o recuperarse juntas.

El objetivo no es eliminar todo acoplamiento. Las partes de un sistema deben colaborar. El objetivo es que las dependencias sean necesarias, explícitas, estables y proporcionales a la responsabilidad compartida. Un acoplamiento bajo pero artificial puede introducir duplicación o inconsistencias; uno alto y oculto hace que el cambio sea impredecible.

Una señal práctica de acoplamiento excesivo es que un cambio local obliga a modificar muchas partes no relacionadas, coordinar despliegues innecesarios o conocer detalles internos de otro módulo.

## 4. Cohesion

La **cohesion**, o cohesión, es el grado en que los elementos de un módulo pertenecen juntos porque contribuyen a una responsabilidad común. Un módulo cohesivo tiene una razón clara para existir.

La **cohesión funcional** aparece cuando los elementos colaboran para completar una capacidad. La **cohesión de datos** aparece cuando reglas y datos describen la misma entidad o concepto. La **cohesión temporal** es más débil: agrupa elementos porque se ejecutan en el mismo momento, aunque no compartan una responsabilidad profunda.

Un módulo de baja cohesión acumula reglas que cambián por motivos distintos. Esto dificulta nombrarlo, probarlo y asignar su propiedad. Un módulo muy pequeño tampoco es automáticamente cohesivo: dividir una responsabilidad en fragmentos artificiales puede aumentar el acoplamiento y dispersar el conocimiento.

Cohesión y acoplamiento deben evaluarse juntos. Una modularidad útil busca alta cohesión interna y acoplamiento externo controlado. La pregunta central es: **¿qué cambios deberían permanecer juntos y qué cambios deberían poder evolucionar por separado?**

## 5. Separation of concerns

La **separation of concerns**, o separación de responsabilidades, es el principio de organizar un sistema para que preocupaciones diferentes no queden mezcladas sin necesidad. Una preocupación es una regla, capacidad, política o aspecto que requiere atención durante el diseño o el cambio.

Ejemplos de preocupaciones son autenticación, autorización, persistencia, presentación, notificaciones, cálculo de resultados y reglas de negocio. Separarlas no significa que nunca interactúen; significa que sus responsabilidades y decisiones no se confunden.

La separación puede aplicarse en distintos niveles:

- entre interfaz de usuario y lógica de negocio;
- entre reglas del dominio y mecanismos técnicos;
- entre políticas de acceso y operaciones funcionales;
- entre escritura de información y generación de vistas de consulta;
- entre un proceso principal y tareas secundarias.

Una separación eficaz reduce el número de razones por las que una parte debe cambiar. Sin embargo, una separación excesiva puede crear capas vacías, indirecciones y contratos innecesarios. El principio debe servir a la comprensión y al control del cambio, no convertirse en una obligación mecánica.

## 6. Dependency direction

La **dependency direction**, o dirección de dependencias, indica qué módulo conoce o necesita a otro. Una dependencia debe apuntar hacia una responsabilidad más estable o hacia una abstracción que proteja al consumidor frente a detalles variables.

Una **abstracción** es una representación que expresa lo esencial de una responsabilidad sin exponer su implementación concreta. Una **interfaz** puede servir como abstracción cuando define el contrato que necesita un consumidor. La dirección correcta depende del objetivo: el código que contiene reglas importantes no debería quedar subordinado a detalles técnicos cambiantes sin una razón clara.

La **inversión de dependencias** es un principio según el cual las políticas de alto nivel no deben depender directamente de detalles de bajo nivel; ambos deben depender de abstracciones. Además, las abstracciones no deben depender de detalles: los detalles deben depender de abstracciones.

Esto no exige que toda relación use interfaces ni que se eliminen todas las dependencias concretas. Exige decidir qué parte debe permanecer estable y cuál puede cambiar. Por ejemplo, una regla de negocio puede expresar que necesita guardar una entidad mediante un contrato, mientras una implementación concreta adapta ese contrato a una base de datos. La dirección protege la regla frente al mecanismo.

Los ciclos de dependencia son especialmente peligrosos. Si A depende de B, B de C y C de A, ningún límite posee claramente la responsabilidad y un cambio puede propagarse por todo el conjunto. Romper el ciclo puede requerir mover una responsabilidad, invertir una interfaz o redefinir el límite.

## 7. Layered architecture

Una **layered architecture**, o arquitectura en capas, organiza el sistema en niveles con responsabilidades diferenciadas y una dirección de dependencia predominantemente vertical. Cada capa ofrece servicios a la capa superior y utiliza servicios de la inferior.

Una organización frecuente incluye:

- **Capa de presentación:** recibe solicitudes y construye respuestas para usuarios o clientes.
- **Capa de aplicación:** coordina casos de uso y transacciones, sin concentrar necesariamente todas las reglas del dominio.
- **Capa de dominio:** contiene conceptos, reglas y decisiones propias del problema.
- **Capa de infraestructura:** proporciona persistencia, red, archivos, mensajería y otros mecanismos técnicos.

La ventaja principal es la familiaridad y la separación de responsabilidades. Las capas pueden facilitar la sustitución de mecanismos, el razonamiento local y la asignación de trabajo. El riesgo aparece cuando se convierten en conductos obligatorios para cualquier operación o cuando la capa de dominio termina dependiendo de detalles inferiores.

Una **capa** es un límite de organización, no una garantía de independencia. Si todas las capas comparten datos, reglas y decisiones, la estructura visual puede ocultar un acoplamiento fuerte. También puede aparecer una arquitectura de capas "anémica", donde las reglas reales se dispersan en controladores, repositorios o interfaces sin un propietario claro.

## 8. Hexagonal architecture

La **hexagonal architecture**, también llamada **ports and adapters architecture**, organiza el sistema alrededor del dominio y de los casos de uso, aislándolos de los mecanismos externos.

Un **port**, o puerto, es una interfaz que expresa cómo el núcleo del sistema necesita interactuar con el exterior. Un puerto de entrada expone una capacidad del sistema a un actor o adaptador; un puerto de salida expresa una necesidad del núcleo, como guardar datos o publicar una notificación.

Un **adapter**, o adaptador, es una implementación que traduce entre un puerto y una tecnología, protocolo o sistema externo. Puede adaptar una solicitud HTTP a un caso de uso, o una interfaz de persistencia a una base de datos concreta.

La arquitectura hexagonal busca que el núcleo dependa de puertos y que los adaptadores dependan de esos contratos. Así, las reglas pueden probarse sin levantar toda la infraestructura y los mecanismos externos pueden cambiar con menor impacto.

El hexágono es una metáfora para mostrar que puede haber múltiples entradas y salidas; no prescribe seis partes ni una forma visual obligatoria. Su valor depende de que los puertos representen necesidades reales y no sean capas artificiales creadas para satisfacer un patrón.

## 9. Clean architecture

La **clean architecture**, o arquitectura limpia, es una familia de principios que organiza el sistema en círculos o anillos de responsabilidad, con las reglas de negocio hacia el centro y los detalles externos hacia el borde.

El principio de dependencia de este estilo indica que las dependencias del código deben apuntar hacia políticas y reglas más estables. Las entidades y casos de uso no deberían depender de la interfaz de usuario, de una base de datos o de un framework concreto. Los detalles externos implementan contratos que necesita el núcleo.

Una organización típica distingue:

- **Entidades:** reglas centrales de conceptos del dominio, relativamente independientes del caso de uso.
- **Casos de uso:** reglas de aplicación que coordinan una capacidad y sus condiciones.
- **Adaptadores de interfaz:** traducciones entre formatos externos y modelos internos.
- **Frameworks y detalles:** mecanismos de entrega, persistencia, comunicación e infraestructura.

Clean architecture y arquitectura hexagonal comparten la intención de proteger el núcleo y controlar la dirección de dependencias. No son recetas incompatibles ni sinónimos exactos: cada una ofrece un vocabulario y una forma de estructurar el mismo problema. La decisión debe basarse en la complejidad y los cambios esperados, no en la obligación de reproducir todos sus anillos.

## 10. Bounded contexts

Un **bounded context**, o contexto delimitado, es un límite explícito dentro del cual un modelo, vocabulario y significado son coherentes. El mismo término puede representar conceptos distintos en contextos diferentes.

El concepto proviene del **Domain-Driven Design**, una disciplina que conecta el diseño del software con el lenguaje y las reglas del dominio. Un **modelo de dominio** es una representación de conceptos y relaciones relevantes para un área del problema. Un **lenguaje ubicuo** es el vocabulario compartido por las personas del dominio y el equipo para hablar de ese modelo.

Un contexto delimitado no es necesariamente un servicio, una base de datos o un equipo, aunque puede relacionarse con ellos. Su función principal es evitar que un modelo se extienda artificialmente a situaciones donde cambia el significado. Dos contextos pueden usar la palabra "curso" para referirse a una oferta publicada o a una inscripción individual; forzarlos a compartir exactamente la misma estructura puede producir confusión.

Entre contextos existen relaciones y contratos. Un contexto puede ser proveedor de información, consumidor, traductor o parte de una integración. Hacer explícito el límite ayuda a decidir qué datos se comparten, qué se transforma y quién es responsable de cada regla.

## 11. Monolithic architecture y modular monolith

Un **monolith**, o monolito, es una aplicación desplegada como una unidad operativa principal. El término describe principalmente la forma de empaquetar y ejecutar el sistema, no la calidad interna de sus límites.

Un **modular monolith**, o monolito modular, es una aplicación desplegada como una unidad, pero organizada internamente en módulos con responsabilidades, interfaces y dependencias explícitas. Puede ofrecer muchos beneficios de modularidad sin introducir desde el inicio la complejidad de la red y de la operación distribuida.

Un monolito puede ser modular o estar fuertemente acoplado. La unidad de despliegue no determina si el código tiene buenos límites. Un monolito modular facilita probar el sistema, comprender la propiedad de cada capacidad y extraer un módulo en el futuro si existe una razón real para hacerlo.

Sus límites deben ser más que convenciones: idealmente se protegen mediante paquetes, reglas de dependencia, APIs internas, pruebas y propiedad clara de los datos. Si todos los módulos pueden leer y modificar todo, el nombre "modular" no describe una propiedad efectiva.

## 12. Monolith vs. microservices

Los **microservices**, o microservicios, son servicios pequeños y autónomos alrededor de capacidades de negocio, que pueden desplegarse y operarse de manera independiente. Cada servicio posee un límite, una interfaz y, en muchos modelos, responsabilidad sobre sus propios datos.

Comparar monolito y microservicios no es comparar una opción buena con una mala. Son formas distintas de distribuir responsabilidades, despliegues, fallos, datos y equipos.

Un monolito puede ofrecer:

- llamadas internas sin latencia de red;
- transacciones y pruebas más simples;
- menor costo operativo inicial;
- despliegue y observabilidad centralizados;
- evolución rápida cuando el equipo y el dominio aún están aprendiendo.

Los microservicios pueden ofrecer:

- despliegue independiente de capacidades;
- aislamiento de ciertas cargas o fallos;
- escalado diferenciado;
- autonomía de equipos con límites claros;
- tecnologías distintas cuando existe una necesidad justificada.

A cambio, introducen contratos de red, latencia, fallos parciales, observabilidad distribuida, coordinación de versiones, consistencia entre datos, seguridad de servicio a servicio y mayor carga operativa. Separar un sistema en procesos no crea automáticamente buenos límites; solo hace más costosas las dependencias incorrectas.

La decisión debe considerar dominio, equipos, carga, atributos de calidad, capacidad operativa, frecuencia de cambio y reversibilidad. Un monolito modular suele ser una opción prudente cuando se necesita claridad y evolución, pero no existe todavía una razón concreta para distribuir el sistema.

## 13. Cómo evaluar límites y modularidad

Una **responsabilidad** es una obligación coherente que una parte del sistema debe cumplir. Un límite arquitectónico es útil cuando agrupa responsabilidades que cambian juntas y separa las que cambian por motivos distintos.

Para evaluar una propuesta de módulos, conviene preguntar:

1. ¿Cada módulo tiene un propósito que puede expresarse en una frase?
2. ¿Sus reglas y datos pertenecen al mismo concepto o capacidad?
3. ¿Qué cambios deberían permanecer dentro del módulo?
4. ¿Qué información necesita exponer y cuál puede ocultar?
5. ¿Quién posee y modifica sus datos?
6. ¿Las dependencias forman una dirección clara o hay ciclos?
7. ¿Las interfaces expresan necesidades estables o detalles internos?
8. ¿El límite ayuda a probar, operar y evolucionar el sistema?
9. ¿La separación responde a una necesidad real o solo a un patrón?
10. ¿Qué costo introduce mantener este límite?

Un **boundary**, o límite, no es bueno por ser pequeño ni malo por ser grande. Es bueno cuando reduce el impacto de cambios relevantes y mantiene comprensible la colaboración entre partes. Los límites deben revisarse con escenarios concretos: publicar una capacidad, corregir una regla, cambiar una integración, consultar datos o recuperarse de un fallo.

## 14. Método práctico de diseño arquitectónico

Un proceso inicial puede seguir estos pasos:

1. **Partir de requisitos y cambios:** identificar capacidades, atributos y decisiones que probablemente evolucionen.
2. **Agrupar por responsabilidad:** reunir reglas y datos que deben permanecer coherentes.
3. **Nombrar módulos:** usar vocabulario del dominio y describir la responsabilidad de cada uno.
4. **Definir contratos:** establecer qué ofrece cada módulo y qué necesita de otros.
5. **Elegir la dirección:** proteger políticas estables frente a mecanismos variables y romper ciclos.
6. **Seleccionar una estructura:** valorar capas, puertos y adaptadores, arquitectura limpia u otra combinación proporcional.
7. **Probar escenarios de cambio:** comprobar si una modificación cruza demasiados límites.
8. **Evaluar el costo de distribución:** decidir si el beneficio requiere procesos independientes o puede lograrse dentro de un monolito modular.
9. **Registrar decisiones y dudas:** documentar razones, alternativas, consecuencias y supuestos pendientes.

Este método no obliga a elegir un estilo puro. Las arquitecturas reales suelen combinar capas, puertos, módulos y contextos delimitados. Lo importante es que la combinación tenga una razón comprensible y que las reglas de dependencia sean observables.

## Cierre

La arquitectura define límites para controlar el cambio. La modularidad hace posible que las partes del sistema mantengan responsabilidades claras, colaboren mediante contratos y evolucionen sin propagar cada decisión a todo el conjunto. Cohesión, acoplamiento, separación de responsabilidades y dirección de dependencias son criterios para juzgar esos límites, no objetivos aislados.

Las arquitecturas en capas, hexagonales y limpias ofrecen formas de organizar responsabilidades; los contextos delimitados ayudan a preservar significados; y el monolito modular ofrece una forma de obtener límites fuertes sin pagar prematuramente el costo de distribuir el sistema.

La pregunta central de esta semana es: **¿qué debe cambiar junto, qué debe permanecer separado y qué dependencias estamos dispuestos a aceptar?** La respuesta prepara el análisis de la Semana 4, donde los límites dejarán de ser solo conceptuales y deberán enfrentarse a redes, latencia y fallos parciales.
