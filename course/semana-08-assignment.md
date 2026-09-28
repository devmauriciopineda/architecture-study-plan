# Assignment Semana 8. Objetivos de resiliencia de SkillHub

## Propósito

Definir una primera política de disponibilidad y recuperación para el MVP de SkillHub, conectando los fallos relevantes con respuestas operativas y objetivos medibles.

## Entregable

Crea `course/semana-08-resilience-analysis.md` con una tabla de escenarios para: indisponibilidad del sistema de empleados, fallo de entrega de notificaciones, pérdida del almacenamiento de documentos y caída de la aplicación durante una evaluación.

Para cada escenario indica dominio de fallo, health check, timeout, reintentos, circuit breaker, degradación permitida, redundancia o recuperación necesaria, y el efecto sobre datos y usuarios. Propón un SLI, un SLO provisional, RTO y RPO para el servicio principal, justificando los supuestos.

## Criterios de aceptación

- Distingue disponibilidad, confiabilidad, resiliencia y tolerancia a fallos.
- Explica qué fallos pueden aislarse y cuáles exigen recuperación ante desastres.
- No permite que la degradación viole permisos, integridad de evaluaciones o trazabilidad.
- Diferencia SLI, SLO y SLA, y formula objetivos observables.
- Incluye al menos tres preguntas pendientes sobre RTO, RPO, respaldos y horario operativo.
- Define cómo se comprobaría que una recuperación realmente funciona.

## Fuera de alcance

No elijas proveedor, topología, herramienta de monitoreo ni procedimiento técnico detallado. El resultado esperado es un conjunto de objetivos y políticas revisables antes de estudiar las alternativas de infraestructura de la semana siguiente.