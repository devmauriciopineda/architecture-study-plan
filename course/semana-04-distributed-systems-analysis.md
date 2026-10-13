# Semana 4. Análisis de fallos distribuidos de SkillHub

## Alcance de la interacción

El análisis cubre la validación de una asignación dirigida a un empleado, área o rol. Antes de activar una asignación, SkillHub necesita confirmar con el sistema corporativo de empleados que la persona existe, está activa y conserva el área o rol que la hace elegible.

La interacción es una **consulta remota de solo lectura**. No se modifica el sistema corporativo de empleados. Si la consulta no permite confirmar la elegibilidad, SkillHub no debe inventar el dato ni activar silenciosamente la asignación.

## Propiedad del estado, operación y datos

| Elemento | Propietario | Tratamiento |
|---|---|---|
| Nombre, área, rol y estado activo del empleado | Sistema corporativo de empleados | Es la fuente maestra. SkillHub solo conserva una representación local limitada para identificar y autorizar operaciones. |
| Asignación, fecha límite y estado de validación | SkillHub | SkillHub decide si queda activa, rechazada o pendiente de validación. |
| Solicitud de validación | SkillHub | Contiene empleado objetivo, criterio de elegibilidad, motivo de la consulta y correlación de la operación. |
| Respuesta de elegibilidad | Sistema corporativo de empleados | Devuelve identidad, estado activo, área, rol y una marca temporal o versión que permita conocer su actualidad. |
| Resultado de la asignación | SkillHub | Se informa como activa solo después de una validación aceptable; los demás resultados quedan explícitos como rechazados o pendientes. |

La operación de consulta es idempotente: repetirla no debe cambiar el estado del sistema corporativo. La activación de la asignación sí es una operación con efecto local y debe ejecutarse una sola vez para la combinación de empleado, curso y campaña, aunque lleguen respuestas duplicadas.

## Latencia y timeout provisional

Para una operación interactiva de una sola asignación, el objetivo provisional es recibir una respuesta en **hasta 2 segundos en el percentil 95**, bajo carga normal. Se propone un **timeout de 3 segundos por consulta**. El valor deja un margen sobre el objetivo para variaciones de red y procesamiento, pero evita retener indefinidamente la acción de RR. HH.

Para una asignación masiva, la validación puede continuar como pendiente y no debe bloquear la pantalla ni mantener recursos abiertos por cada empleado. La campaña debe mostrar cuántas personas quedaron activas, rechazadas o pendientes.

Estos valores son hipótesis porque el contrato y la latencia real del sistema de empleados aún deben validarse. El timeout no significa que la consulta haya fallado en el destino: significa únicamente que SkillHub no recibió una respuesta utilizable dentro del límite.

## Modos de fallo y respuesta

| Modo de fallo | Qué puede haber ocurrido | Respuesta de SkillHub |
|---|---|---|
| **Fallo total:** sistema de empleados caído o reiniciándose | La consulta no llega o el proveedor no puede procesarla | No activar nuevas asignaciones dependientes de esa validación. Marcar como `Pendiente de validación`, informar el motivo y conservar la solicitud para reintento controlado. Las asignaciones ya activas no se eliminan automáticamente. |
| **Fallo parcial:** red interrumpida en una sola dirección o respuesta perdida | El sistema externo pudo recibir y procesar la consulta, pero SkillHub no recibió el resultado | Tratar el resultado como desconocido, no como “no elegible”. Reintentar una vez si el fallo parece transitorio; si sigue sin respuesta, dejar pendiente. |
| **Saturación o lentitud** | El proveedor está vivo, pero las colas hacen que supere el tiempo esperado | Aplicar el timeout, limitar reintentos y evitar una tormenta de consultas. En una campaña, continuar con otros empleados y contabilizar pendientes. |
| **Respuesta tardía después del timeout** | La consulta pudo completarse en el proveedor y la respuesta llega cuando SkillHub ya cerró la espera | Ignorar una respuesta tardía para activar directamente una asignación si no puede asociarse a una solicitud aún válida. Si se acepta posteriormente, debe pasar por la misma validación de vigencia y control de duplicados. |
| **Respuesta duplicada o consulta repetida** | Un reintento o una repetición de procesamiento produce más de una respuesta | La consulta se procesa como lectura idempotente. La activación local usa una clave lógica única de empleado-curso-campaña y devuelve el resultado ya registrado en vez de crear otra asignación. |
| **Dato desactualizado** | El empleado estaba activo o tenía el rol correcto cuando se consultó, pero cambió después | La respuesta debe incluir su momento de observación. Solo se acepta para activar dentro de una ventana de frescura acordada; un dato vencido lleva a `Pendiente de validación`. No se modifica el dato maestro localmente. |
| **Respuesta incompleta, inválida o incompatible** | Falta estado, área, rol o la respuesta no puede interpretarse con seguridad | Rechazar la validación como no concluyente, registrar el error de contrato y dejar pendiente o rechazar según la política acordada. Nunca completar campos faltantes con valores supuestos. |
| **Respuesta negativa válida** | El empleado no existe, está inactivo o no cumple área/rol | No crear una asignación activa. Registrar `Rechazada` con la razón observable y permitir corregir la población o los datos en el sistema propietario. |

## Reintentos e idempotencia

Se permite **un reintento** únicamente ante timeout, pérdida de respuesta o error que el responsable del sistema externo clasifique como transitorio. Debe existir una espera breve entre intentos y un límite total inferior a la ventana de la operación interactiva. No se reintenta ante una respuesta negativa válida, datos inválidos o una saturación persistente, porque repetir puede aumentar la carga y retrasar la recuperación.

La consulta no necesita una clave de idempotencia para proteger el estado externo porque es de solo lectura. Sí necesita un identificador de correlación para unir solicitud, respuesta, timeout y auditoría. Para la operación local de activación, SkillHub debe usar una clave lógica formada por **empleado + curso + campaña o asignación solicitada**. Si llega dos veces la misma confirmación, el resultado final debe ser la misma asignación activa, no dos asignaciones.

Un timeout posterior a una posible finalización no autoriza a ejecutar dos veces la activación. SkillHub debe separar la consulta de la decisión local: primero determina si existe una respuesta válida y vigente; después aplica una única transición idempotente de la asignación.

## Respuesta desactualizada y política de consistencia

El estado activo, el área y el rol son datos que afectan autorización y elegibilidad. Para una asignación nueva se necesita una política de **consistencia fuerte respecto de la fuente maestra**, al menos dentro de la ventana de frescura acordada: una copia local vieja no puede aprobar por sí sola una nueva asignación.

La disponibilidad se subordina a esa regla concreta del producto. Si el sistema corporativo no responde o la respuesta está vencida, SkillHub sigue disponible para consultar campañas, mostrar asignaciones existentes y registrar solicitudes, pero no activa nuevas asignaciones dependientes de esa validación. Esta elección evita asignar capacitación a empleados inactivos o a una población cuyo rol ya no corresponde.

Una respuesta negativa vigente puede cerrar la operación como `Rechazada`. Una respuesta positiva vigente permite activar. Una respuesta positiva antigua, ambigua o sin marca temporal no es equivalente a una respuesta actual y debe quedar `Pendiente de validación`.

## Comportamiento cuando el sistema externo no está disponible

SkillHub debe:

1. conservar la solicitud y el criterio de población;
2. marcar cada elemento no validado como pendiente, con causa y hora del último intento;
3. informar a RR. HH. cuántas asignaciones fueron activadas, rechazadas y aplazadas;
4. permitir reintento posterior controlado, sin duplicar asignaciones ya activas;
5. mantener disponibles las asignaciones y resultados previamente confirmados;
6. impedir que un usuario edite manualmente el área, rol o estado activo para sortear la indisponibilidad.

No se debe usar el último dato local conocido para activar una asignación nueva si su frescura no está garantizada. Ese dato puede mostrarse como referencia, claramente etiquetado como no verificado, pero no puede convertirse en autorización.

## Invariantes

| Invariante | Condición que no debe violarse | Control o evidencia |
|---|---|---|
| I-01 | Nunca se activa una asignación nueva sin una respuesta positiva, válida y suficientemente reciente del sistema propietario. | Estado `Activa` exige referencia a validación, momento y resultado positivo. |
| I-02 | SkillHub nunca modifica nombre, área, rol ni estado activo del sistema corporativo. | Los datos locales son de consulta o representación sincronizada; no existe edición de usuario sobre ellos. |
| I-03 | Un empleado, curso y campaña no pueden tener dos asignaciones activas equivalentes. | Clave lógica de asignación y transición local idempotente. |
| I-04 | Timeout, respuesta tardía o respuesta incompleta no equivalen a una respuesta negativa. | Estados separados `Pendiente de validación`, `Rechazada` y `Activa`. |
| I-05 | Una respuesta desactualizada no puede autorizar una nueva asignación. | Marca temporal o versión obligatoria y ventana de frescura validada. |
| I-06 | Un reintento no debe producir efectos locales duplicados. | Correlación de solicitud y deduplicación por empleado-curso-campaña. |
| I-07 | Las asignaciones ya activas no se eliminan automáticamente porque una consulta posterior falle. | La indisponibilidad afecta nuevas validaciones; cualquier revocación requiere una decisión explícita basada en datos actuales. |

## Supuestos y preguntas pendientes

1. ¿El sistema corporativo entrega una marca temporal o versión confiable para determinar cuándo una respuesta de empleado deja de ser válida?
2. ¿Cuál es la latencia normal y el límite de carga del sistema externo durante campañas de asignación? Esto confirmará o cambiará el objetivo de 2 segundos y el timeout de 3 segundos.
3. ¿Una asignación masiva debe ser parcialmente aceptable por empleado, como propone este análisis, o RR. HH. exige que toda la campaña sea atómica?
4. ¿Qué estados y códigos permiten distinguir un empleado inexistente, inactivo, no elegible, respuesta incompleta y error transitorio?