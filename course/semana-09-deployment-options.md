# Semana 9. Opciones de despliegue de SkillHub

## 1. Contexto de decisión

SkillHub tiene aproximadamente 1.000 empleados, hasta 100 cursos, documentos asociados y campañas que pueden producir picos de asignaciones y notificaciones. El MVP tiene una restricción explícita: debe poder desplegarse on-premise. También depende del sistema corporativo de empleados y del correo corporativo, y necesita proteger resultados, certificados y documentos.

La comparación no elige proveedor, producto, topología ni herramienta concreta. Evalúa modelos de responsabilidad y deja abierta la validación de costos, conectividad, identidad y recuperación.

## 2. Comparación de modelos

| Criterio | On-premise | Cloud | Híbrido |
|---|---|---|---|
| **Control** | La empresa controla ubicación, hardware, red, almacenamiento y políticas. También responde por capacidad, parches, reemplazos y seguridad física. | El proveedor controla infraestructura física y parte de la plataforma; la empresa controla configuración, código, datos, permisos y uso del servicio. | La empresa conserva control sobre sistemas o datos locales y delega una parte variable; debe coordinar responsabilidades y límites entre ambos entornos. |
| **Escalabilidad** | Principalmente vertical o con capacidad comprada por anticipado; crecer requiere planificación, adquisición y espacio. La cola de notificaciones absorbe picos, pero no crea capacidad infinita. | Aprovisionamiento más elástico y posibilidad de escalar capacidad de aplicación o trabajos variables; el límite puede ser el costo, la cuota o el sistema corporativo externo. | Puede usar cloud para picos, reportes o respaldos y local para capacidades estables, pero la conectividad entre entornos puede convertirse en el cuello de botella. |
| **Seguridad** | Permite mantener datos y documentos dentro de controles corporativos conocidos; la empresa debe operar segmentación, parches, secretos, accesos y respuesta a incidentes. No es automáticamente más seguro. | Ofrece controles y capacidades gestionadas, pero la empresa sigue siendo responsable de identidades, permisos, configuración, datos y errores de aplicación. Un permiso amplio puede exponer información. | Amplía la superficie de confianza: hay que proteger ambos entornos, el enlace, la sincronización y las identidades cruzadas. Un fallo de configuración puede atravesar la frontera. |
| **Operación** | Requiere personal para compute, almacenamiento, red, respaldos, monitoreo, recuperación y mantenimiento. Encaja con capacidades existentes solo si están disponibles y documentadas. | Reduce operación de hardware y parte del mantenimiento, pero exige gobernar consumo, contratos, límites, observabilidad, configuración y cambios del proveedor. | Es la opción más exigente operativamente: dos modelos, coordinación de incidentes, inventario de dependencias y procedimientos de recuperación cruzados. |
| **Costo total** | Mayor costo inicial de capacidad, personal, respaldo y reemplazo; costo variable bajo una vez disponible. El costo de capacidad ociosa puede ser significativo. | Menor inversión inicial, pero costo recurrente variable de ejecución, almacenamiento, transferencia, servicios gestionados, soporte y salida. El costo puede crecer con campañas o reportes. | Añade costos de conectividad, transferencia, operación doble, seguridad y habilidades. Puede evitar ampliar hardware local, pero solo compensa si la variabilidad real lo justifica. |
| **Disponibilidad** | Depende de redundancia local, mantenimiento y dependencias corporativas. Una falla de sala, red o almacenamiento puede afectar todo el servicio si comparte dominio de fallo. | Puede ofrecer opciones de redundancia y capacidad bajo demanda, pero no elimina fallos regionales, de configuración ni del sistema de empleados. | Puede mantener una parte disponible cuando la otra falla, pero una conexión caída puede hacer inaccesibles identidad, datos o documentos. La degradación debe estar definida por flujo. |
| **Recuperación** | La empresa debe mantener copias, responsables, capacidad de restauración y ejercicios. El objetivo provisional es RTO <= 2 h y RPO <= 4 h, aún pendientes de validar frente a respaldos diarios. | El proveedor puede facilitar copias y restauración, pero RTO/RPO reales dependen del servicio contratado, configuración, región y exportación comprobada. | La recuperación debe cubrir dos entornos y su orden de dependencia. Una copia local y otra cloud no sirven si ambas comparten credenciales, red o un dato corrupto. |
| **Networking** | Latencia predecible con el sistema de empleados si están en la red corporativa; la empresa opera rutas, segmentación, firewall, nombres y exposición. | Requiere conectividad segura hacia el sistema corporativo y control de salida/entrada; latencia y transferencia pueden variar. | Necesita un enlace entre entornos, rutas, cifrado, supervisión y comportamiento ante partición. La red se convierte en dependencia crítica del servicio. |
| **Identity** | Puede integrarse con controles corporativos existentes, pero la empresa administra ciclo de vida, recuperación, roles, secretos y auditoría. | Puede delegar parte de identidad en capacidades del proveedor, pero debe coordinarla con el sistema corporativo y evitar identidades duplicadas. | Debe definir una fuente de identidad, sincronización de altas/bajas, confianza entre entornos y comportamiento cuando el enlace no está disponible. |
| **Compute** | Máquinas o entornos virtualizados controlados por la empresa; el dimensionamiento y la capacidad de campaña son responsabilidad local. | IaaS, PaaS o ejecución gestionada permiten ajustar capacidad; la abstracción reduce operación, pero introduce límites y dependencia del proveedor. | Compute puede repartirse según estabilidad o variabilidad, pero la aplicación debe tolerar latencia y fallos entre los dos entornos. |
| **Storage** | Estado operativo y documentos pueden permanecer dentro de la empresa; respaldos y copias fuera del dominio local requieren capacidad y procedimiento propios. | Almacenamiento gestionado puede aportar durabilidad y elasticidad; hay costos por volumen, operaciones, transferencia y recuperación, además de requisitos de exportación. | Permite conservar documentos o datos sensibles localmente y usar cloud para copias o proyecciones, pero la sincronización y la consistencia deben ser explícitas. |
| **Dependencia de proveedor** | Menor dependencia de un proveedor cloud, pero dependencia de hardware, contratos de soporte, personal y sistemas corporativos. | Dependencia fuerte de precios, límites, APIs, formatos y servicios gestionados; migrar puede ser costoso si se usan capacidades específicas. | Dependencia distribuida entre proveedor cloud, TI corporativo y conectividad; puede reducir dependencia única, pero aumenta coordinación y no garantiza portabilidad. |
| **Sistema de empleados** | Integración local directa, con menor latencia si comparte red; el sistema sigue siendo propietario y su indisponibilidad bloquea validaciones nuevas. | Integración remota con más variabilidad; se necesita timeout, circuit breaker y estados pendientes como en semanas 4 y 8. | Puede mantener la consulta cerca del sistema corporativo y mover otras cargas, pero el enlace entre zonas sigue afectando la autorización. |
| **Documentos** | Control directo sobre ubicación, acceso y retención; la empresa debe probar copias y restauración fuera del dominio de fallo. | Servicios de almacenamiento gestionados facilitan durabilidad y distribución, pero exigen controles de acceso, exportación y revisión de costos. | Puede dejar documentos regulados localmente y usar cloud para copias o contenido no sensible; requiere clasificación y sincronización confiable. |

## 3. Responsabilidades de la empresa y del proveedor

### On-premise

La empresa es responsable de hardware, capacidad, sistema operativo o plataforma de ejecución, red, almacenamiento, identidad, parches, seguridad física y lógica, respaldos, restauración, monitoreo y continuidad. El equipo de producto sigue siendo responsable del código, reglas de negocio, contratos, permisos y calidad de datos.

### Cloud

El proveedor es responsable de la infraestructura física y de las partes explícitamente incluidas en cada servicio. La empresa conserva responsabilidad sobre cuentas, identidad, permisos, configuración, datos, documentos, retención, observabilidad, código, contratos, costos y pruebas de recuperación. Un servicio gestionado no elimina la necesidad de verificar que una restauración funcione.

### Híbrido

La responsabilidad se divide por componente, pero también existe una responsabilidad transversal sobre el enlace, identidad federada, sincronización, latencia, compatibilidad y recuperación conjunta. Ninguna de las partes puede asumir que la otra restaurará la experiencia completa de SkillHub.

## 4. Recomendación provisional para el MVP

### On-premise con abstracciones operativas simples

Se recomienda mantener el MVP on-premise porque satisface la restricción explícita, mantiene cerca el sistema corporativo de empleados y evita introducir una dependencia de red adicional antes de validar carga, RTO/RPO, presupuesto y capacidades del equipo. La cola de notificaciones y los estados pendientes permiten absorber picos sin exigir cloud desde el primer día.

Conceptualmente:

- **IaaS o virtualización interna:** usar capacidad de cómputo y almacenamiento provisionada de forma reproducible, sin asumir máquinas físicas dedicadas por módulo.
- **Contenedores:** aceptables para empaquetar la aplicación stateless y los procesadores de trabajos si el equipo ya puede operarlos; no justifican por sí solos una plataforma de orquestación compleja.
- **PaaS:** usarlo solo si la organización ya ofrece una plataforma interna con responsabilidades y recuperación claras; no crear una plataforma propia como parte del MVP.
- **Managed services:** aprovechar servicios gestionados internos para respaldos, almacenamiento o correo cuando existan contratos claros; no convertir “gestionado” en una garantía sin pruebas de restauración.
- **Serverless:** queda fuera del alcance inicial porque la operación on-premise, la carga mayormente estable y el control sobre evaluación, documentos y recuperación no necesitan ese modelo todavía.

### Fuera del alcance de esta decisión

- proveedores o productos concretos;
- elección de una distribución, orquestador o servicio cloud;
- topología detallada de redes, zonas o centros de datos;
- migración inmediata a cloud;
- multi-cloud;
- automatización completa de recuperación ante desastres;
- diseño de archivos IaC reales.

## 5. Infrastructure as Code conceptual

Si la organización dispone de IaC, deberían definirse y versionarse como mínimo:

- ambientes y capacidad de ejecución de la aplicación stateless y los trabajos asíncronos;
- segmentación de red, rutas, reglas de acceso y nombres necesarios;
- almacenamiento operativo, documentos, respaldos y políticas de retención;
- configuración de la cola, límites, reintentos y prioridades;
- identidades de usuarios y cargas de trabajo, roles y permisos mínimos;
- parámetros de observabilidad, health checks y alertas de SLO;
- políticas de backup, restauración y validación de recuperación;
- configuración no secreta de despliegue, dejando secretos en un mecanismo seguro fuera del código.

IaC no debe contener contraseñas ni reemplazar la revisión de permisos. Debe poder reconstruir un entorno conocido, detectar cambios manuales relevantes y documentar qué recursos deben restaurarse antes que otros.

## 6. Riesgo de migración o salida

El riesgo principal es el **lock-in operativo y de datos**: si una futura migración depende de formatos de almacenamiento, colas, identidad o servicios gestionados específicos, exportar datos y reproducir garantías puede costar más que mover el código. El riesgo existe incluso usando un único proveedor on-premise si los procesos manuales no están documentados.

Mitigaciones:

1. Mantener ownership y contratos de datos independientes de productos concretos.
2. Preferir formatos exportables y conservar una copia verificable de cursos, documentos, resultados, certificados y eventos de auditoría.
3. Mantener la aplicación stateless y separar estado de negocio de la ejecución para poder reconstruir el entorno.
4. Versionar configuración e IaC, y probar una restauración en un entorno alternativo.
5. Documentar el procedimiento de salida, tiempos, datos no portables y responsabilidades, en vez de asumir que la portabilidad será automática.

Multi-cloud no es automáticamente la mejor solución: duplicaría networking, identity, storage, observabilidad, controles, contratos y habilidades, y podría multiplicar los costos de recuperación. No elimina la dependencia del sistema corporativo de empleados ni del correo. Solo sería justificable ante una exigencia concreta de disponibilidad, regulación, negociación o capacidad que un único modelo no pueda satisfacer.

## 7. Consecuencias aceptadas

1. **Elasticidad limitada:** on-premise puede requerir capacidad comprada antes del pico y no crecer tan rápido como cloud; se acepta mientras las campañas y la concurrencia real estén dentro de las hipótesis.
2. **Responsabilidad operativa local:** la empresa debe operar respaldos, capacidad, parches y recuperación; se acepta porque la restricción on-premise exige asumir ese control y hacerlo visible.
3. **Menor portabilidad inmediata:** optimizar para la infraestructura corporativa puede dificultar una migración futura; se mitiga con contratos, IaC, formatos exportables y pruebas de reconstrucción, no con multi-cloud preventivo.
4. **Recuperación provisional:** los respaldos diarios existentes pueden no alcanzar el RPO provisional de cuatro horas; se acepta como brecha pendiente, no como garantía de producción.

## 8. Supuestos y preguntas pendientes

1. **Costos:** ¿Cuál es el costo disponible para hardware, almacenamiento, soporte, respaldos, personal y reemplazo durante tres años, y cómo se compara con un costo cloud variable?
2. **Conectividad:** ¿Qué latencia, disponibilidad y límites existen entre SkillHub, el sistema de empleados, el correo y cualquier entorno alternativo que pudiera evaluarse?
3. **Identity:** ¿Qué sistema es fuente de identidad, cómo se revocan usuarios inactivos y qué ocurre con autorización y recuperación cuando la conexión corporativa falla?
4. **Recuperación:** ¿La infraestructura on-premise tiene un dominio de fallo alternativo, copias recuperables y personal disponible para cumplir RTO <= 2 h y RPO <= 4 h?
5. **Documentos:** ¿Qué clasificación, retención, tamaño y requisitos de restauración tienen los adjuntos y certificados, y qué parte puede salir de las instalaciones sin incumplir política?