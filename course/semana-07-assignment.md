# Assignment Semana 7. Análisis de capacidad de SkillHub

## Propósito

Identificar el primer cuello de botella probable de SkillHub durante una campaña de capacitación y proponer una intervención de escalabilidad o rendimiento basada en evidencia y supuestos explícitos.

## Entregable

Crea `course/semana-07-capacity-analysis.md` con un diagrama sencillo del recorrido de una solicitud y una tabla para estos escenarios: consulta del catálogo, revisión de contenidos y evaluación, y envío de notificaciones.

Para cada escenario indica carga normal y pico, latencia y throughput objetivo provisionales, recurso limitante, y si aplicarías escalado vertical u horizontal, arquitectura stateless, balanceador, caché, CDN, connection pooling, backpressure, cola o particionamiento. Justifica cada mecanismo y señala qué métrica demostraría su necesidad.

## Criterios de aceptación

- Distingue latencia, throughput, concurrencia y capacidad.
- Identifica al menos un cuello de botella y explica cómo comprobarlo.
- Incluye una política para absorber un pico sin agotar recursos.
- Considera datos privados al proponer caché o CDN.
- Define tres supuestos o preguntas pendientes sobre usuarios simultáneos, campañas y tamaños de contenidos.
- Compara el costo o riesgo de la propuesta con mantener una solución más simple.

## Fuera de alcance

No hagas pruebas de carga reales, no elijas infraestructura ni fijes cifras definitivas. El resultado esperado es un modelo inicial de capacidad que pueda validarse antes de implementar optimizaciones.