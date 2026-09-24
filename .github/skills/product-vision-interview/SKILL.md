---
name: product-vision-interview
description: "Crear o actualizar un documento product-vision.md para una solución de software nueva mediante una entrevista guiada con askQuestions. Usar cuando no existe documentación previa del producto, cuando hay una idea inicial que necesita definición, o cuando se requiere aclarar problema, usuarios, valor, alcance y éxito antes de construir."
argument-hint: "Describe brevemente la idea de solución, organización o problema que quieres convertir en visión de producto"
user-invocable: true
---

# Entrevista de visión de producto

## Objetivo

Convertir una idea inicial de software, sin documentación previa suficiente, en un documento `product-vision.md` claro, útil para alinear producto, diseño, arquitectura y desarrollo.

La salida debe expresar decisiones de producto y sus límites. No debe convertirse en una especificación técnica detallada ni inventar información que la persona entrevistada no haya confirmado.

## Cuándo usar

- Se planea construir una solución nueva y todavía no existe documentación confiable.
- Hay una idea, problema u oportunidad que necesita convertirse en una visión compartida.
- Se necesita revisar una visión existente porque contiene supuestos, alcance o criterios de éxito ambiguos.
- Se quiere preparar el contexto mínimo antes de diseñar arquitectura o planificar un MVP.

## Flujo

### 1. Revisar el contexto disponible

1. Lee la solicitud inicial y extrae la idea, dominio, organización y restricciones ya conocidas.
2. Comprueba si existe `product/product-vision.md` o un documento equivalente.
3. Si existe, trátalo como borrador: conserva información confirmada, marca contradicciones y pregunta solo por vacíos o decisiones que deban cambiar.
4. No conviertas documentación técnica existente en requisitos de producto sin validación.

### 2. Entrevistar con `askQuestions`

Usa `askQuestions` para entrevistar a la persona. Haz una pregunta por turno, con opciones cuando ayuden a responder y permitiendo texto libre para matices. No presentes todo el cuestionario de una vez.

Adapta las preguntas según las respuestas. Omite preguntas ya resueltas, profundiza en respuestas vagas y señala explícitamente los supuestos que podrían afectar el producto.

Cubre, como mínimo, estas preguntas esenciales:

1. **Situación y problema:** ¿qué situación concreta ocurre hoy, para quién y qué consecuencias tiene? Pide ejemplos observables, no solo una solución deseada.
2. **Usuarios y compradores:** ¿quién usa la solución, quién se beneficia, quién decide o paga y quién opera el producto?
3. **Necesidad prioritaria:** ¿qué resultado necesita lograr cada usuario y por qué las alternativas actuales no son suficientes?
4. **Propuesta de valor:** ¿qué cambio medible ofrece la solución y qué la hace preferible frente a no hacer nada o usar otra alternativa?
5. **Casos de uso principales:** ¿cuáles son los tres a cinco flujos que deben funcionar para que el producto sea útil?
6. **Alcance inicial:** ¿qué debe incluir el MVP o primera versión para validar valor? Separa imprescindible, deseable y posterior.
7. **Fuera de alcance:** ¿qué queda explícitamente excluido para proteger foco y evitar expectativas incorrectas?
8. **Éxito:** ¿cómo sabremos que el producto funciona? Define indicadores, comportamiento esperado y, cuando sea posible, una meta o línea base.
9. **Restricciones:** ¿qué condiciona la solución: presupuesto, plazo, regulación, privacidad, seguridad, tecnología, integraciones, operación o capacidad del equipo?
10. **Dependencias y riesgos:** ¿de qué sistemas, datos, decisiones o actores depende el producto? ¿Qué supuestos podrían invalidar la visión?
11. **Horizonte:** ¿qué evolución se imagina después de validar la primera versión y qué principios no deben romperse?

### 3. Confirmar antes de redactar

Antes de escribir, resume las decisiones en lenguaje breve y pide confirmación con `askQuestions`:

- problema y usuario prioritarios;
- propuesta de valor;
- alcance del MVP;
- fuera de alcance;
- indicadores de éxito;
- restricciones y supuestos de mayor riesgo.

Si una respuesta permanece desconocida, escribe `Por definir` y registra la pregunta como decisión pendiente. No rellenes el vacío con una suposición silenciosa.

### 4. Redactar `product/product-vision.md`

Crea o actualiza el archivo usando esta estructura, ajustándola al dominio:

```markdown
# Visión de producto: [Nombre]

## Resumen
## Problema
## Oportunidad
## Usuarios y actores
## Necesidades y casos de uso principales
## Propuesta de valor
## Objetivo de producto
## Alcance del MVP
## Fuera del MVP
## Resultado esperado
## Indicadores iniciales de éxito
## Principios de producto
## Contexto, restricciones y dependencias
## Supuestos y riesgos
## Visión a futuro
## Decisiones pendientes
```

Reglas de redacción:

- Escribe en el idioma de la entrevista, salvo que la persona pida otro.
- Sé específico sobre quién, qué problema y qué resultado; evita frases genéricas como “mejorar la experiencia”.
- Distingue hechos confirmados, objetivos, restricciones, supuestos y decisiones pendientes.
- Usa listas para alcance, exclusiones, indicadores, riesgos y principios.
- No agregues arquitectura, endpoints, modelos de datos ni tecnologías salvo que sean una restricción de producto confirmada.
- Mantén el MVP pequeño y comprobable; cada elemento debe justificar qué hipótesis valida o qué necesidad resuelve.
- Si se actualiza un documento existente, preserva contenido confirmado y evita cambios cosméticos no relacionados.

### 5. Revisar y validar

Después de redactar:

1. Comprueba que el problema puede entenderse sin conocer la solución.
2. Comprueba que cada usuario principal tiene una necesidad y un resultado asociado.
3. Comprueba que el MVP y el fuera de alcance no se contradicen.
4. Comprueba que cada indicador mide un resultado relevante y no solo actividad.
5. Comprueba que restricciones, dependencias y riesgos están visibles.
6. Comprueba que no quedan decisiones críticas ocultas en lenguaje ambiguo.
7. Si faltan datos críticos, vuelve a preguntar antes de declarar el documento terminado.

## Criterio de finalización

La tarea termina cuando:

- `product/product-vision.md` existe en la ubicación acordada;
- problema, usuarios, valor, MVP, exclusiones y éxito están expresados de forma concreta;
- los supuestos no confirmados aparecen como tales;
- la persona entrevistada confirmó el resumen de decisiones;
- no hay contradicciones evidentes entre alcance, restricciones e indicadores.

Al finalizar, informa brevemente qué documento se creó o actualizó, qué decisiones quedaron definidas y qué preguntas permanecen abiertas.
