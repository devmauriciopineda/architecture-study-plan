# Assignment Semana 4. Análisis de fallos distribuidos de SkillHub

## Propósito

Aplicar los fundamentos de sistemas distribuidos a una interacción de SkillHub que dependa del sistema corporativo de empleados o de la entrega de notificaciones, sin diseñar todavía APIs ni infraestructura.

## Entregable

Crea `course/semana-04-distributed-systems-analysis.md` con un análisis de 1 a 2 páginas sobre **consulta de datos del empleado para validar una asignación**. Incluye:

- propietario del estado, operación y datos que se intercambian;
- latencia y timeout provisional, con justificación;
- al menos cinco modos de fallo, incluyendo un timeout después de que la operación pudo completarse;
- decisión sobre reintentos e idempotencia;
- impacto de una respuesta desactualizada y política de consistencia necesaria;
- comportamiento esperado si el sistema externo no está disponible;
- una tabla breve de invariantes que no deben violarse.

## Criterios de aceptación

- Distingue fallo total, fallo parcial, respuesta tardía y dato desactualizado.
- Explica qué puede hacer SkillHub sin corromper asignaciones ni modificar datos maestros.
- Justifica cuándo reintentar y cuándo rechazar o dejar pendiente la operación.
- Identifica si cada operación relevante es idempotente y propone una clave o control cuando sea necesario.
- Relaciona la decisión de consistencia y disponibilidad con una regla concreta del producto.
- Declara al menos tres supuestos o preguntas pendientes para validar con el responsable del sistema de empleados.

## Fuera de alcance

No definas endpoints, tecnologías de mensajería, topología de despliegue, tablas ni una solución completa de recuperación ante desastres. El resultado esperado es una política razonada y revisable para la siguiente semana, cuando se estudiarán APIs y comunicación.