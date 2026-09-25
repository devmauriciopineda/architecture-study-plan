# Assignment Semana 3. Límites y modularidad de SkillHub

## Propósito

Proponer una primera estructura modular para SkillHub, justificando responsabilidades, contratos y dirección de dependencias antes de implementar.

## Entregable

Crea `course/semana-03-architecture-boundaries.md` con:

- entre 5 y 8 módulos o contextos delimitados, cada uno con nombre, propósito, responsabilidades y datos que posee;
- una explicación de qué cambios deberían permanecer dentro de cada módulo;
- las interfaces o contratos principales entre módulos, indicando quién depende de quién;
- una vista de dependencias, en tabla o diagrama Mermaid, sin incluir clases, endpoints ni detalles de infraestructura;
- una comparación breve entre implementar la propuesta como monolito modular y como microservicios;
- la decisión provisional recomendada y tres consecuencias aceptadas.

## Criterios de aceptación

- Cada módulo tiene una responsabilidad coherente y no es solo una capa técnica.
- La propuesta distingue reglas de negocio, mecanismos externos y adaptadores.
- Las dependencias tienen dirección explícita y no forman ciclos sin justificación.
- Se identifican al menos dos límites de contexto donde un mismo término podría tener significados diferentes.
- La comparación monolito modular/microservicios considera latencia, fallos, operación, despliegue, consistencia y costo.
- La decisión se relaciona con los requisitos, la carga y las restricciones documentadas en las semanas anteriores.

No implementes módulos ni elijas frameworks. El resultado debe ser una propuesta razonada y revisable, no una arquitectura definitiva.
