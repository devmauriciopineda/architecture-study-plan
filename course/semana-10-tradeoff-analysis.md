# Semana 10. Análisis de trade-offs para notificaciones de SkillHub

## 1. Capacidad y escenario

La capacidad evaluada es **gestionar notificaciones** de asignaciones, resultados relevantes, certificados y decisiones de revisión. Una notificación debe aparecer en la bandeja interna y puede generar un envío por correo corporativo. El hecho de negocio no debe depender de que el correo sea entregado: una asignación, resultado o certificado confirmado conserva su estado aunque la comunicación quede pendiente o fallida.

### Escenario de referencia

- Población inicial aproximada: 1.000 empleados y hasta 100 cursos.
- Una campaña puede producir cerca de 1.000 notificaciones en una ráfaga; cifra todavía hipotética.
- La bandeja interna es parte de la experiencia de SkillHub y debe respetar autorización y auditoría.
- El correo corporativo es una dependencia externa y puede estar lento o indisponible.
- Se requiere estado explícito: `Pendiente`, `Entregada`, `Fallida` o `No procesable`.
- Se requieren deduplicación, reintentos limitados, backpressure y reconstrucción o reprocesamiento controlado.
- La solución debe poder desplegarse inicialmente on-premise.

## 2. Drivers y restricciones

### Drivers

1. **Integridad:** no duplicar efectos ni presentar un correo como entregado si no lo fue.
2. **Confiabilidad:** aislar la indisponibilidad del correo de asignaciones, evaluaciones y certificados.
3. **Rendimiento:** aceptar una ráfaga sin bloquear las operaciones interactivas; el productor solo espera aceptación durable del trabajo.
4. **Operabilidad:** diagnosticar edad de mensajes, profundidad de cola, reintentos y mensajes no procesables.
5. **Costo proporcional:** resolver el escenario del MVP sin pagar capacidad o complejidad para una escala no demostrada.
6. **Evolución:** permitir agregar canales o plantillas sin cambiar cada módulo de negocio.

### Restricciones y supuestos

- El sistema de empleados sigue siendo la fuente maestra de identidad organizacional; notificaciones no pueden crear permisos propios.
- Los mensajes pueden contener datos personales o referencias a resultados y certificados; el consumidor debe aplicar autorización y minimización de datos.
- La plataforma inicial es on-premise y el correo corporativo pertenece a otra responsabilidad operativa.
- Se asume que existe o puede obtenerse una capacidad corporativa para entregar correo, pero sus límites, disponibilidad y contrato aún no están confirmados.
- Las cifras de volumen, tasa de rebote y retención son hipótesis y deben medirse antes de dimensionar.

## 3. Alternativas comparadas

### A. Módulo mínimo construido en SkillHub

La aplicación conserva el estado de notificación y usa una cola durable interna sencilla, con un worker que entrega a la bandeja y al adaptador del correo corporativo. Los eventos de negocio se registran con identificador único; el worker aplica reintentos limitados y deduplica por evento, destinatario, canal y tipo.

### B. Capacidad gestionada adquirida

SkillHub mantiene el contrato de notificación y delega cola, entrega, reintentos y parte de la observabilidad a un servicio gestionado aprobado por la organización. La bandeja interna y los estados de negocio siguen siendo responsabilidad de SkillHub. Esta alternativa puede requerir conectividad externa, por lo que no se considera disponible hasta validar la restricción on-premise, seguridad y salida.

### C. Plataforma de mensajería autogestionada

La empresa opera una plataforma de cola o mensajería durable separada de SkillHub, junto con workers y adaptadores de entrega. Permite mayor aislamiento, varios consumidores y más capacidad futura, pero agrega un componente crítico que debe parchearse, monitorearse, respaldarse y recuperarse.

## 4. Matriz de decisión

Escala cualitativa: **bajo** es favorable en costo o complejidad; **alto** es favorable en rendimiento, confiabilidad, escalabilidad y flexibilidad, salvo que se indique lo contrario. Las valoraciones son hipótesis de arquitectura, no resultados de una prueba.

| Criterio | A. Módulo mínimo construido | B. Capacidad gestionada adquirida | C. Mensajería autogestionada |
|---|---|---|---|
| **Costo inicial** | Bajo: reutiliza el monolito y una capacidad interna simple. | Medio: integración, contrato, configuración y posible conectividad de salida. | Alto: infraestructura, instalación, automatización y conocimiento operativo. |
| **Costo total** | Bajo a medio para el volumen inicial; sube con soporte, retención, recuperación y nuevas garantías. | Medio y variable: tarifa, transferencia, soporte, integración, monitoreo y salida. Puede ser favorable si reduce personal operativo. | Alto: capacidad permanente, parches, guardias, observabilidad, backups, ejercicios de recuperación y upgrades. |
| **Rendimiento** | Suficiente para la ráfaga hipótesis si la aceptación es rápida y el worker está desacoplado; puede competir por recursos con la aplicación. | Potencialmente alto y elástico, sujeto a límites, latencia de red y cuota del servicio. | Alto y controlable; puede aislar recursos y ajustar consumidores, con costo de configuración. |
| **Confiabilidad** | Adecuada si la cola es durable, los efectos son idempotentes y se prueba restauración; comparte dominio de fallo con SkillHub. | Puede ofrecer durabilidad y reintentos, pero la garantía real depende del contrato y de una recuperación validada. | Puede ofrecer aislamiento y control, pero la empresa asume que la plataforma realmente conserva y recupera mensajes. |
| **Complejidad operativa** | Baja a media: estados, worker, reintentos, métricas y procedimiento de reconstrucción. | Media: integración, credenciales, límites, costos, incidentes y coordinación con proveedor. | Alta: cluster o capacidad equivalente, despliegues, upgrades, seguridad, backups, capacidad y recuperación. |
| **Escalabilidad** | Suficiente para 1.000 empleados y campañas previstas; requiere medir antes de separar o ampliar. | Alta en teoría, pero limitada por contrato, conectividad y dependencia del servicio. | Alta con inversión y operación adicional; permite más consumidores y canales. |
| **Flexibilidad** | Media: contratos internos y adaptadores permiten agregar canales, pero el módulo conoce más detalles operativos. | Media o alta según APIs y extensiones; puede encerrar semántica de entrega y plantillas en el servicio. | Alta: control sobre consumidores, retención, prioridad y contratos, a cambio de mantenerlos. |
| **Deuda técnica** | Media y visible: la cola simple puede requerir migración si crecen volumen, consumidores o garantías. Se puede registrar una fecha y métrica de revisión. | Media: deuda de integración, contrato y salida; las capacidades no visibles del servicio pueden quedar como supuestos. | Alta inicialmente: operación especializada, automatización y procedimientos que no son diferenciadores del producto. |
| **Dependencia de proveedor** | Baja dependencia de un proveedor de mensajería; sigue dependiendo del correo corporativo y de la infraestructura interna. | Alta: API, formato, límites, precio, transferencia y disponibilidad del proveedor. | Baja de un proveedor externo de mensajería, pero dependencia alta de personal, hardware y soporte interno. |
| **Reversibilidad** | Alta para sustituir el worker o la cola si el contrato de SkillHub permanece estable; media si se mezclan detalles de la implementación con el dominio. | Media a baja: cambiar contratos, exportar mensajes pendientes y reproducir garantías puede ser costoso. | Media: la aplicación puede conservar contratos estables, pero migrar mensajes, configuración y operación exige una transición planificada. |
| **Recuperación y salida** | La empresa debe reconstruir estados y mensajes desde eventos o auditoría; la salida es sencilla si los datos son exportables. | Exige exportar mensajes, estados, plantillas y configuración; el proveedor puede cobrar transferencia o no exponer todos los datos. | La empresa controla exportación, pero debe mantener formatos, respaldos y capacidad alternativa; una migración operativa puede ser laboriosa. |

## 5. Trade-offs y consecuencias por alternativa

### A. Módulo mínimo construido

**Beneficios:** es compatible con on-premise, reduce tiempo inicial, mantiene el contrato y los datos cerca de SkillHub y permite controlar la bandeja interna, la deduplicación y la auditoría. Una cola acotada protege las operaciones principales frente a campañas.

**Riesgos:** la cola o worker puede competir por recursos con la aplicación; el equipo debe implementar estados, reintentos, backpressure, métricas y recuperación. Si se incorporan muchos canales o consumidores sin límites, la solución pequeña se convierte en una plataforma accidental.

**Consecuencias aceptadas:** se acepta menor elasticidad y una posible migración futura porque el volumen inicial es moderado y no está demostrado que una plataforma separada sea necesaria. Se acepta operar el correo como dependencia externa, siempre que el fallo quede visible y no revierta el hecho de negocio.

**Revisión:** migrar o separar si la edad máxima de mensajes supera el objetivo en dos campañas consecutivas, si la profundidad de cola amenaza el p95 de las operaciones interactivas, si se necesitan más de dos consumidores independientes o si la restauración no cumple el RTO/RPO acordado.

### B. Capacidad gestionada adquirida

**Beneficios:** reduce código operativo propio, puede proporcionar durabilidad, reintentos y escalabilidad, y permite que el equipo se concentre en contratos de notificación y reglas de negocio.

**Riesgos:** agrega vendor lock-in, costos variables, latencia de red, límites de cuota y una dependencia de identidad y conectividad. El proveedor puede reportar aceptación sin que SkillHub pueda probar la entrega final. Una capacidad gestionada no resuelve automáticamente autorización, retención, auditoría ni recuperación de la bandeja.

**Consecuencias aceptadas:** se acepta dependencia contractual si el costo total de operar internamente supera el beneficio de control y existe exportación verificable. No se acepta que la disponibilidad del servicio gestionado se confunda con la disponibilidad de SkillHub.

**Revisión:** considerar esta alternativa si el costo de operación propia supera el presupuesto validado, si el volumen o los canales crecen por encima de la capacidad interna, o si el servicio ofrece garantías de recuperación y salida que puedan probarse en el entorno on-premise.

### C. Plataforma de mensajería autogestionada

**Beneficios:** separa recursos, permite múltiples consumidores, prioridades y retención controladas, y reduce la dependencia de un proveedor externo. Puede convertirse en una capacidad compartida para otras aplicaciones corporativas.

**Riesgos:** crea un producto operativo adicional antes de demostrar la necesidad. Una falla de configuración, actualización, capacidad o respaldo puede afectar todas las notificaciones. La empresa asume el costo de especialización, guardias, seguridad y recuperación.

**Consecuencias aceptadas:** solo se justificaría aceptar la complejidad si existe una necesidad corporativa más amplia que comparta el costo o si las métricas muestran que el módulo mínimo es un límite real. No se acepta construirla únicamente para anticipar una escala hipotética.

**Revisión:** considerar esta alternativa si varias aplicaciones requieren la misma capacidad, si hay consumidores independientes con objetivos incompatibles, o si las campañas sostenidas exceden la capacidad del módulo sin poder resolver el límite mediante más workers y recursos locales.

## 6. Trade-offs relacionados

### Costo frente a rendimiento

Una cola y worker dentro del monolito requieren menos infraestructura, pero consumen recursos compartidos. Aumentar workers o separar la plataforma puede mejorar throughput, aunque añade operación. No se justifica pagar por elasticidad antes de medir latencia, profundidad de cola y duración de campañas.

### Costo frente a confiabilidad

Backups más frecuentes, almacenamiento durable, deduplicación y ejercicios de restauración cuestan tiempo y recursos. El gasto está justificado para no perder avisos auditables, pero no todos los mensajes necesitan el mismo tratamiento: el hecho de negocio y la bandeja deben conservarse; un correo fallido puede reprocesarse.

### Complejidad frente a escalabilidad

La plataforma autogestionada escala mejor en escenarios amplios, pero introduce estados, dependencias y procedimientos operativos. La alternativa mínima escala verticalmente y con workers mientras la carga sea moderada. El límite debe demostrarse con una prueba, no inferirse del volumen futuro.

### Consistencia frente a disponibilidad

La entrega de correo puede ser eventual: `Pendiente` es un resultado válido. La creación de la asignación, el certificado y la notificación interna no deben depender de una respuesta final del correo. El estado de notificación debe ser consistente e idempotente para evitar duplicados, aunque el canal externo esté caído.

### Build frente a buy

Se debe construir la lógica que diferencia a SkillHub: qué hecho genera una notificación, quién puede verla, qué datos mínimos contiene y cómo se audita. Se puede comprar o reutilizar la entrega, cola o capacidad de transporte cuando sea aprobada, observable y exportable. Comprar no elimina la integración, la gestión de identidad ni la prueba de recuperación.

### Managed frente a self-managed

Managed reduce tareas rutinarias, pero traslada parte del riesgo a contrato, costo y salida. Self-managed aumenta control y portabilidad, pero obliga a operar capacidad, parches, seguridad, backups y recuperación. Para el MVP, el volumen conocido no compensa asumir una plataforma de mensajería completa.

## 7. Costo total de propiedad

La comparación debe incluir más que la infraestructura:

| Componente de costo | A. Módulo mínimo | B. Gestionado adquirido | C. Autogestionado |
|---|---|---|---|
| Construcción e integración | Estado, worker, adaptador y métricas. | Contratos, SDK o integración, credenciales y pruebas de compatibilidad. | Integración más automatización de plataforma, workers y procedimientos. |
| Operación y soporte | Recursos compartidos, monitoreo y atención de fallos de correo. | Gobierno de servicio, cuotas, facturación, incidentes y coordinación con proveedor. | Personal especializado, guardias, parches, upgrades y capacidad permanente. |
| Datos y almacenamiento | Retención de estados, eventos y auditoría dentro del modelo de SkillHub. | Cargos por mensajes, almacenamiento, operaciones y transferencia; posible costo de exportación. | Discos, copias, replicación y crecimiento de la plataforma. |
| Recuperación | Restaurar estado y reconstruir trabajos desde eventos o auditoría. | Verificar RTO/RPO del servicio, exportar datos y recuperar ante caída del proveedor. | Respaldar plataforma y datos, reconstruir capacidad y ejecutar ejercicios propios. |
| Seguridad y cumplimiento | Secretos, permisos, auditoría y protección de datos en la infraestructura interna. | Revisión contractual, identidad externa, configuración y ubicación de datos. | Endurecimiento, rotación, segmentación, parches y evidencia de operación. |
| Salida o migración | Sustituir worker o cola detrás del contrato estable. | Reescribir integración, extraer estados y asumir transferencia o restricciones del proveedor. | Migrar configuración, mensajes, datos y conocimiento operativo a otra capacidad. |

La cifra monetaria queda pendiente porque faltan presupuesto, tarifas, volumen real, costo horario del equipo y objetivos formales de recuperación. La matriz evita fingir precisión y deja identificadas las partidas que deben medirse.

## 8. Recomendación provisional

Elegir **A: módulo mínimo construido en SkillHub**, con una cola durable interna y workers separados lógicamente de la ruta interactiva. Mantener un contrato de notificación independiente de la implementación permite sustituir más adelante la cola por una capacidad gestionada o una plataforma corporativa sin cambiar los módulos de asignaciones, aprendizaje o certificación.

La recomendación no significa construir un sistema genérico de mensajería. El alcance inicial debe limitarse a:

- eventos de asignación, resultado, certificado y decisión de revisión;
- bandeja interna y adaptador de correo corporativo;
- estados explícitos, deduplicación y reintentos limitados;
- backpressure, cola de no procesables y métricas de edad, profundidad y tasa de entrega;
- reconstrucción o reprocesamiento controlado desde eventos y auditoría;
- autorización de lectura y trazabilidad de cada cambio de estado.

Una capacidad gestionada es la primera alternativa de evolución si el costo operativo o la carga crecen. La plataforma autogestionada queda reservada para una necesidad compartida por varias aplicaciones, no para una escala hipotética de SkillHub.

## 9. Reversibilidad y decisiones difíciles de cambiar

### Decisiones reversibles

- sustituir el worker o aumentar su cantidad;
- cambiar límites de reintento, prioridad y retención dentro de políticas aprobadas;
- agregar un adaptador de canal detrás del contrato;
- mover la generación de plantillas fuera del módulo si el contrato de datos se conserva;
- reemplazar la cola interna por una capacidad gestionada mediante un adaptador probado.

### Decisiones difíciles de cambiar

- qué significa cada estado y cuándo una entrega cuenta como confirmada;
- ownership de la bandeja, eventos, auditoría y preferencias;
- identificador de deduplicación y semántica de reintento;
- datos personales incluidos en mensajes y reglas de autorización;
- formatos de exportación, retención y reconstrucción;
- acoplar la lógica de negocio a una API propietaria o asumir que la entrega externa es parte de la transacción.

Por ello, se debe invertir más análisis en contratos, ownership y salida que en elegir una tecnología de cola para el primer despliegue.

## 10. Preguntas pendientes y validaciones

1. **Volumen y rendimiento:** ¿Cuál es la ráfaga máxima real por campaña, cuál es el límite de entrega del correo y qué p95 de aceptación y edad máxima de mensaje necesita RR. HH.?
2. **Costo operativo:** ¿Cuántas horas mensuales puede dedicar TI a operar workers, respaldos, incidentes y ejercicios de recuperación, y cuál es el presupuesto para una capacidad gestionada?
3. **Recuperación:** ¿Qué RTO/RPO aplican a la bandeja, estados de entrega, eventos y auditoría, y desde qué fuente se reconstruyen mensajes tras perder la cola?
4. **Identidad y privacidad:** ¿Qué datos puede contener una notificación, cómo se valida el destinatario cuando cambia de rol o queda inactivo y qué ocurre si el sistema de empleados no responde?
5. **Salida:** ¿El servicio corporativo o gestionado permite exportar estados, mensajes pendientes, plantillas, auditoría y configuración en formatos utilizables sin sus APIs?
6. **Alcance corporativo:** ¿Hay otras aplicaciones que necesiten la misma mensajería y puedan justificar una plataforma compartida, o SkillHub sería el único consumidor durante el horizonte del MVP?

## 11. Criterio de revisión

La decisión debe revisarse al completar una prueba con una campaña representativa y después de las primeras campañas reales. La revisión debe incluir profundidad y edad máxima de cola, p95 de aceptación, porcentaje de entregas, mensajes no procesables, costo mensual, incidentes operativos y resultado de una restauración. Si esos datos incumplen objetivos o revelan consumidores y volúmenes nuevos, se prepara un ADR de evolución hacia una capacidad gestionada o una plataforma autogestionada.