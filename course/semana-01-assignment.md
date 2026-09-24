# Assignment Semana 1. Mapa sistémico de la aplicación

## Propósito

Aplicar el pensamiento sistémico para describir la aplicación real como un sistema, antes de tomar decisiones de arquitectura o implementación.

## Entregable

Crea `course/semana-01-system-map.md` con un análisis breve, claro y revisable que contenga:

1. **Propósito del sistema:** qué resultado produce la aplicación y para quién.
2. **Límite del sistema:** qué queda dentro, qué queda fuera y qué elementos están en la frontera.
3. **Actores y stakeholders:** objetivo, información aportada o recibida y preocupación principal de cada uno.
4. **Entradas y salidas:** al menos cinco flujos de información, indicando origen, destino y transformación principal.
5. **Dependencias:** sistemas, procesos o equipos de los que depende la aplicación; incluye propietario y efecto de una indisponibilidad o cambio.
6. **Feedback loops:** un bucle reforzador y uno balanceador, o una explicación justificada si no existen todavía.
7. **Complejidad y restricciones:** tres fuentes de complejidad y al menos cinco restricciones, distinguiendo duras, blandas, explícitas e implícitas.
8. **Requisitos:** cinco requisitos funcionales y cinco no funcionales. Los no funcionales deben incluir una métrica o condición verificable cuando sea posible.

Puedes usar una tabla o un diagrama sencillo en Mermaid, pero cada diagrama debe acompañarse de una explicación textual. No diseñes todavía clases, endpoints, tablas ni infraestructura detallada: el objetivo es comprender el sistema y sus relaciones.

## Criterios de aceptación

- El límite distingue claramente responsabilidades propias y dependencias externas.
- Cada actor se describe por su objetivo, no solo por su nombre.
- Los flujos muestran movimiento y transformación de información, no únicamente llamadas entre componentes.
- Las dependencias incluyen consecuencias ante cambios o fallos.
- Los requisitos son observables y no mezclan una capacidad con una preferencia tecnológica.
- El documento termina con tres preguntas abiertas o supuestos que deban validarse durante el desarrollo.

## Resultado esperado

Un mapa sistémico suficientemente preciso para detectar omisiones, discutir el alcance y preparar la identificación de requisitos y restricciones de la semana 2.
