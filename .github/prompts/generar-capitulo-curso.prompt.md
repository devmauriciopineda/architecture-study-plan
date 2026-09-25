---
name: generar-capitulo-curso
description: Genera el capítulo académico y el assignment práctico de una semana del plan de estudio de arquitectura de software.
argument-hint: Indica la semana que quieres desarrollar, por ejemplo: Semana 2
---

# Generar capítulo de curso

Desarrolla los materiales de estudio correspondientes a la semana indicada por el usuario: **$ARGUMENTS**.

## Contexto que debes consultar

Antes de escribir:

1. Lee `architecture_systems_thinking_12_weeks.md` y localiza el objetivo y todos los temas de la semana solicitada.
2. Lee `product/product-vision.md` y `product/product-requirements.md` para entender el proyecto real que acompaña el curso.
3. Revisa los archivos existentes en `course/` para conservar el idioma, la nomenclatura, la profundidad y el estilo editorial del curso.
4. Si existe un capítulo o assignment de la semana solicitada, actualízalo cuidadosamente en lugar de crear otro archivo duplicado.

Si la semana no existe en el temario, detente y solicita una semana válida. No inventes temas que no estén en el plan, aunque puedes introducir conceptos auxiliares necesarios para explicar los temas principales.

## Entregables

Crea o actualiza exactamente estos dos archivos dentro de `course/`:

- `semana-NN-<slug-del-tema>.md`: capítulo oficial del curso.
- `semana-NN-assignment.md`: assignment de aplicación práctica.

Usa dos dígitos para `NN` y un slug corto, estable y legible. Conserva los nombres de archivos existentes cuando estés actualizando materiales.

## Requisitos del capítulo

Escribe el capítulo en español, con estilo de libro de texto y como parte de un curso técnico oficial. Debe ser:

- académico, riguroso y conceptualmente claro;
- genérico: no debe resolver ni describir una arquitectura concreta del proyecto;
- orientado a la aplicación práctica, porque se estudiará mientras se desarrolla un software real;
- legible en aproximadamente 10 a 20 minutos;
- consistente con las lecciones anteriores y posteriores.

Incluye, como mínimo:

1. Un título que identifique la semana y el tema.
2. Un propósito u objetivo de lectura.
3. El desarrollo ordenado de todos los conceptos enumerados en el temario de esa semana.
4. La definición de cada término técnico mencionado, incluso cuando se use como subtítulo o en una lista.
5. Explicaciones de relaciones, diferencias, supuestos y consecuencias prácticas entre los conceptos.
6. Ejemplos genéricos de software, sistemas o situaciones arquitectónicas, sin usar SkillHub ni otro proyecto específico como caso principal.
7. Un método, marco de análisis o secuencia de aplicación cuando sea útil.
8. Un cierre que conecte la lección con la siguiente etapa del plan.

No conviertas el capítulo en una lista de definiciones. Explica los conceptos en prosa, establece relaciones entre ellos y señala límites o matices importantes. Evita detalles de implementación que pertenezcan a semanas posteriores, salvo que sean imprescindibles para aclarar el concepto actual.

## Requisitos del assignment

El assignment debe ser breve, de máximo una página en Markdown, y debe aplicar los contenidos de la semana al proyecto real descrito en los documentos de producto. Debe incluir:

- propósito del ejercicio;
- entregable concreto, con ruta y nombre de archivo;
- pasos o preguntas que obliguen a aplicar los conceptos de la semana;
- criterios de aceptación observables;
- resultado esperado y, cuando corresponda, preguntas abiertas o supuestos a validar.

El assignment debe complementar el capítulo, no repetirlo. Tiene que ser realizable durante el desarrollo del proyecto y debe evitar exigir decisiones que el temario aún no haya preparado. Cuando sea pertinente, indica explícitamente qué queda fuera del ejercicio para controlar el alcance.

## Reglas editoriales y de calidad

- Usa Markdown limpio y encabezados jerárquicos.
- Escribe en español técnico, preciso y natural.
- Usa terminología consistente con el plan y los documentos del producto.
- No agregues dependencias, código de aplicación ni archivos auxiliares.
- No introduzcas requisitos, actores o capacidades del proyecto que contradigan la documentación existente.
- No incluyas una explicación de tu proceso ni una respuesta conversacional extensa: crea los dos archivos y después resume brevemente qué generaste.
- Después de escribir, verifica que ambos archivos existan, que el capítulo cubra todos los temas de la semana y que el assignment respete la extensión máxima solicitada.
