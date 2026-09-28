# Assignment Semana 6. Mapa de datos y workloads de SkillHub

## Propósito

Aplicar arquitectura de datos al MVP de SkillHub, distinguiendo datos operativos, datos maestros y datos destinados a reportes antes de elegir almacenamiento o mecanismos de optimización.

## Entregable

Crea `course/semana-06-data-architecture-analysis.md` con un mapa y una tabla para estos grupos: cursos y contenidos, asignaciones y progreso, intentos y resultados, certificados, datos de empleados y reportes.

Para cada grupo indica propietario, consumidores, ciclo de vida, workload OLTP o analítico, consistencia requerida, transacciones relevantes, necesidad de replicación, caché o pipeline, y comportamiento si la fuente no está disponible. Justifica relacional o NoSQL solo cuando exista una necesidad concreta.

## Criterios de aceptación

- Ningún dato tiene dos fuentes maestras sin una justificación explícita.
- Se distinguen datos actuales, históricos y derivados.
- Se explican al menos una transacción crítica y un caso donde la consistencia eventual sea aceptable o no lo sea.
- Cada propuesta de caché, réplica, partición o pipeline relaciona el mecanismo con un workload.
- Se documentan retención, corrección y eliminación de al menos dos grupos de datos.
- Incluye tres supuestos o preguntas pendientes sobre volumen, reportes y contrato del sistema de empleados.

## Fuera de alcance

No elijas un motor concreto, no diseñes tablas completas ni implementes migraciones. El resultado esperado es una propuesta de ownership y garantías que pueda contrastarse con las decisiones de escalabilidad de la semana siguiente.