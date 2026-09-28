# Assignment Semana 9. Comparación de modelos de despliegue para SkillHub

## Propósito

Comparar modelos de infraestructura para el MVP de SkillHub y hacer explícitas las responsabilidades, restricciones y dependencias que acompañan cada alternativa.

## Entregable

Crea `course/semana-09-deployment-options.md` con una tabla comparativa de tres opciones: on-premise, cloud e híbrida. Evalúa control, escalabilidad, seguridad, operación, costo total, disponibilidad, recuperación, networking, identity, compute, storage y dependencia de proveedor.

Para la opción recomendada, indica si usarías conceptualmente IaaS, PaaS, managed services, contenedores o serverless; qué quedaría fuera del alcance; y qué recursos deberían definirse mediante Infrastructure as Code. Incluye un riesgo de migración o salida y una estrategia para mitigarlo.

## Criterios de aceptación

- La comparación usa los mismos criterios para las tres opciones.
- Distingue responsabilidades de la empresa y del proveedor.
- Considera el despliegue on-premise exigido por el MVP, la integración con el sistema de empleados y el almacenamiento de documentos.
- Explica por qué multi-cloud no es automáticamente la mejor solución.
- Identifica al menos tres supuestos pendientes sobre costos, conectividad, identidad y recuperación.
- La recomendación es provisional y enumera consecuencias aceptadas.

## Fuera de alcance

No elijas proveedores, productos concretos, una topología detallada ni archivos IaC reales. El resultado esperado es una comparación argumentada que prepare la evaluación de trade-offs de la semana siguiente.