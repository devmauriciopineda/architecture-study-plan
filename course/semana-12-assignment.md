# Assignment Semana 12. Ruta evolutiva de SkillHub

## Propósito

Planificar una evolución arquitectónica incremental para SkillHub sin interrumpir el MVP ni comprometer sus reglas de negocio y datos.

## Entregable

Crea `course/semana-12-evolution-roadmap.md` con un estado actual resumido y una ruta de tres etapas para una capacidad: reportes, notificaciones, integración con el sistema de empleados o gobierno de cursos.

Para cada etapa indica objetivo, cambio de ownership, compatibilidad necesaria, datos que se migran o duplican, mecanismo de observación, riesgo, criterio de finalización y ruta de retorno. Evalúa si conviene pasar de monolito a monolito modular, aplicar Strangler pattern o extraer un servicio. Incluye dos architecture fitness functions y una deuda técnica temporal con condición explícita de retiro.

## Criterios de aceptación

- La ruta evita una reescritura total y define estados intermedios verificables.
- Cada etapa conserva compatibilidad y explica qué ocurre con consumidores antiguos.
- Se distingue una mejora de monolito modular de una extracción a servicios.
- Las fitness functions miden propiedades concretas y tienen una respuesta ante incumplimiento.
- Se identifican riesgos de datos, operación y reversión, con criterios de finalización.
- Incluye al menos tres supuestos o preguntas pendientes sobre crecimiento, contratos y prioridades del producto.

## Fuera de alcance

No implementes la migración, no diseñes código de compatibilidad ni cambies el ADR original. El resultado esperado es una hoja de ruta revisable que conecte evolución, métricas y decisiones anteriores.