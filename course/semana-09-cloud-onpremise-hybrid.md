# Semana 9. Arquitectura cloud, on-premise e híbrida

## Propósito

Comprender cómo el lugar y el modelo de operación de la infraestructura cambian las decisiones de arquitectura. Al terminar la lectura podrás comparar on-premise, cloud, híbrido y multi-cloud, distinguir IaaS de PaaS y servicios gestionados, y evaluar contenedores, serverless, networking, compute, storage, identity e Infrastructure as Code sin confundir una tecnología con una estrategia.

## El modelo de despliegue como decisión

La infraestructura es el conjunto de recursos físicos y lógicos que ejecutan, conectan, almacenan y protegen un sistema. La arquitectura de despliegue define quién posee esos recursos, quién los opera, cómo se amplían, cómo se recuperan y qué responsabilidades quedan fuera del equipo de producto.

**On-premise** describe una infraestructura instalada y operada dentro de las instalaciones o centros de datos de la organización. La empresa controla hardware, redes, almacenamiento, ubicación y políticas de acceso, pero también asume compra, mantenimiento, capacidad, reemplazos, seguridad física y recuperación. On-premise no significa automáticamente más seguro ni más barato: el resultado depende de las capacidades y procesos disponibles.

**Cloud** es un modelo de consumo de recursos informáticos bajo demanda, con aprovisionamiento mediante interfaces, capacidad elástica y cobro asociado al uso o a la reserva. El proveedor opera parte de la infraestructura física y ofrece distintos niveles de servicio. Cloud puede acelerar la provisión y facilitar cambios de capacidad, pero introduce dependencia de proveedor, costos variables, límites de servicio y responsabilidades compartidas.

Una arquitectura **híbrida** combina infraestructura on-premise y cloud dentro de una solución coordinada. Puede conservar datos o sistemas regulados localmente y usar cloud para capacidad variable, respaldo o servicios especializados. El beneficio exige resolver conectividad, identidad, latencia, transferencia de datos, observabilidad y recuperación entre entornos. Híbrido no es simplemente tener servidores en dos lugares; es gestionar una dependencia entre ellos.

**Multi-cloud** es el uso deliberado de más de un proveedor cloud. Puede responder a requisitos de disponibilidad, regulación, negociación o capacidades específicas, pero suele duplicar redes, controles, habilidades, observabilidad y automatización. Evitar el lock-in no es suficiente razón: la portabilidad real tiene costo y puede reducir el aprovechamiento de servicios especializados.

## Niveles de servicio: IaaS y PaaS

**IaaS** (Infrastructure as a Service) ofrece recursos fundamentales como máquinas virtuales, redes y discos para que el cliente instale y opere una parte amplia de la plataforma. Proporciona control y flexibilidad, pero mantiene responsabilidades sobre sistema operativo, configuración, parches, capacidad y seguridad de las cargas.

**PaaS** (Platform as a Service) ofrece una plataforma gestionada para ejecutar aplicaciones sin que el equipo tenga que administrar todos los detalles del sistema operativo o de la infraestructura subyacente. Puede incluir entornos de ejecución, bases de datos, colas o servicios de despliegue. Reduce trabajo operativo y acelera entrega, a cambio de aceptar restricciones, costos y una dependencia más fuerte del proveedor.

IaaS y PaaS son puntos de un continuo de control y responsabilidad, no categorías que vuelvan innecesaria la arquitectura. En ambos casos el equipo debe definir configuración, datos, permisos, observabilidad, límites, recuperación y comportamiento ante fallos. Delegar la operación de un componente no delega la responsabilidad sobre el servicio que el producto ofrece.

## Managed services

Un **managed service** o servicio gestionado es una capacidad operada parcial o totalmente por un proveedor, como una base de datos, un almacén de objetos, una cola o una plataforma de identidad. El proveedor se ocupa de tareas definidas en el contrato, como parches, disponibilidad de nodos o copias, mientras el cliente conserva otras responsabilidades.

Los servicios gestionados pueden reducir mantenimiento y permitir que equipos pequeños se concentren en el dominio. También tienen límites de configuración, precios por operación, políticas de retención, ventanas de mantenimiento y mecanismos de exportación que deben comprenderse. “Gestionado” no equivale a “sin operación”: hay que configurar, actualizar contratos, vigilar consumo, probar restauraciones y controlar accesos.

La elección debe comparar el costo total: infraestructura, personal, soporte, migración, observabilidad, transferencia, recuperación y salida del proveedor. Un servicio barato por unidad puede resultar costoso por volumen o por una dependencia difícil de sustituir.

## Contenedores y serverless

Un **contenedor** empaqueta una aplicación con sus dependencias en una unidad aislada que comparte el kernel del sistema anfitrión. Facilita reproducibilidad entre entornos, distribución y despliegue consistente. No es una máquina virtual completa: el aislamiento, la seguridad, el almacenamiento persistente y la red requieren decisiones explícitas.

Los contenedores ayudan a estandarizar la ejecución, pero no resuelven automáticamente escalabilidad, observabilidad, secretos, datos ni recuperación. También pueden ocultar diferencias entre desarrollo y producción si la imagen, la configuración y los recursos no se controlan.

**Serverless** es un modelo en el que el proveedor administra la infraestructura de ejecución y cobra o asigna capacidad según invocaciones, tiempo o recursos consumidos. Puede incluir funciones activadas por eventos y servicios gestionados. El equipo se concentra en código y contratos, pero acepta límites de duración, arranque, estado, depuración, concurrencia y dependencia del entorno.

Serverless no significa que no existan servidores ni que siempre sea más barato. Es adecuado cuando el trabajo es variable, acotado y compatible con ejecución bajo demanda. Una carga constante, una operación prolongada o una necesidad de control específico puede favorecer otro modelo. La comparación debe incluir latencia de arranque, costo mínimo, límites y portabilidad.

## Kubernetes, conceptualmente

**Kubernetes** es una plataforma de orquestación que coordina contenedores mediante un estado declarado. Decide dónde ejecutar cargas, reinicia instancias, distribuye tráfico y permite definir escalado, configuración y descubrimiento de servicios. Su abstracción principal es describir el estado deseado para que el sistema trabaje hacia él.

Kubernetes puede aportar consistencia operativa cuando hay muchas cargas, equipos o entornos que necesitan automatización común. También introduce complejidad de control plane, redes, almacenamiento, actualizaciones, seguridad, monitoreo y conocimiento especializado. Conceptualmente, debe evaluarse como una plataforma operativa, no como una forma obligatoria de empaquetar cualquier aplicación.

Un clúster de Kubernetes no elimina las decisiones de dominio ni la necesidad de diseñar stateless, límites, datos, timeouts y recuperación. Si el problema es una aplicación pequeña con carga conocida, el costo de la plataforma puede superar su beneficio.

## Networking

**Networking** o conectividad es el conjunto de redes, rutas, nombres, protocolos, firewalls y controles que permiten que clientes, aplicaciones, servicios y operadores se comuniquen. El diseño debe considerar latencia, ancho de banda, segmentación, exposición, cifrado, resolución de nombres y dependencia de enlaces.

Una red bien segmentada limita el alcance de un incidente y separa tráfico público, de aplicación, de datos y de administración. Cada salto y filtro puede aumentar latencia o convertirse en un punto de fallo. En arquitecturas híbridas o multi-cloud también hay que definir quién opera la conexión, cómo se supervisa y qué ocurre si se interrumpe.

## Compute, storage e identity

**Compute** es la capacidad de procesamiento que ejecuta código, ya sea en máquinas virtuales, contenedores, funciones o servidores físicos. La selección depende de CPU, memoria, aceleradores, duración del trabajo, concurrencia y necesidad de control. Compute debe dimensionarse y limitarse para evitar que una carga consuma todos los recursos.

**Storage** es la capacidad de conservar datos. Puede ser almacenamiento de bloques para discos, de archivos para sistemas compartidos u objetos para documentos y contenido direccionado por identificadores. Cada modalidad tiene garantías, latencia, costo, durabilidad, límites de tamaño y formas distintas de respaldo. Elegir storage exige distinguir datos temporales, estado operativo, documentos y copias de recuperación.

**Identity** o identidad es la representación verificable de usuarios, servicios y dispositivos. Incluye autenticación, autorización, roles, credenciales, federación, ciclo de vida y auditoría. En un entorno cloud, la identidad controla tanto el acceso de personas como el de cargas de trabajo a redes, datos y servicios. Una mala distribución de permisos puede convertir un error de aplicación en una exposición amplia.

La identidad debe seguir el principio de mínimo privilegio: cada actor recibe solo los permisos necesarios y durante el tiempo necesario. En una arquitectura híbrida, hay que definir qué sistema es fuente de identidad, cómo se sincronizan altas y bajas y qué sucede cuando la conexión entre entornos falla.

## Infrastructure as Code

**Infrastructure as Code** (IaC, infraestructura como código) es la definición declarativa o programática de recursos de infraestructura en archivos versionados y revisables. Permite repetir entornos, registrar cambios, automatizar provisión y comparar el estado esperado con el real.

IaC no es una copia automática de la consola ni garantiza configuraciones seguras. El código debe gestionar secretos sin exponerlos, controlar permisos, ordenar dependencias, validar cambios y definir cómo se destruyen recursos. También necesita revisión, pruebas, estados compartidos y un procedimiento para corregir una desviación manual.

El valor principal es convertir decisiones operativas en artefactos reproducibles. Una definición que no puede restaurar una red, una identidad o un almacenamiento importante está incompleta. La granularidad debe ser suficiente para repetir y auditar sin hacer imposible entender el sistema.

## Marco de decisión

Aplica esta secuencia al comparar modelos de despliegue:

1. **Define responsabilidades.** Lista qué debe controlar la organización y qué capacidad puede delegar sin perder garantías.
2. **Clasifica restricciones.** Considera datos, regulación, conectividad, operación, recuperación, personal disponible y dependencia de proveedor.
3. **Describe la carga.** Identifica variabilidad, crecimiento, latencia, storage, compute, tráfico y necesidad de escalado.
4. **Compara modelos.** Evalúa on-premise, cloud, híbrido y multi-cloud con el mismo conjunto de criterios: control, seguridad, costo total, operación, portabilidad y resiliencia.
5. **Elige el nivel de abstracción.** Decide entre IaaS, PaaS, managed services, contenedores, serverless o una combinación justificada.
6. **Diseña las fronteras.** Define networking, identity, datos, observabilidad y recuperación antes de mover una carga.
7. **Prueba reversibilidad.** Documenta cómo exportar datos, reconstruir recursos, cambiar de proveedor o continuar si un enlace o servicio está indisponible.

## Cierre

Cloud, on-premise e híbrido son modelos de responsabilidad y operación, no destinos arquitectónicos universales. IaaS, PaaS, managed services, contenedores y serverless cambian cuánto se administra, pero no eliminan los problemas de identidad, datos, redes, costos y recuperación. En la siguiente semana estas alternativas se evaluarán mediante trade-offs explícitos entre costo, rendimiento, confiabilidad, complejidad y dependencia de proveedores.