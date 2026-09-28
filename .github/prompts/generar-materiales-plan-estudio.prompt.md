---
name: generar-materiales-plan-estudio
description: Elabora los documentos de estudio de un curso sobre cualquier tema a partir de su objeto y temario.
argument-hint: Indica el objeto de estudio y pega o referencia el temario del curso.
---

# Generar materiales de plan de estudio

Elabora los materiales correspondientes al curso cuyo objeto y temario proporciona el usuario:

**Objeto de estudio:** $ARGUMENTS

Si el usuario proporciona el temario en un archivo, léelo antes de escribir. Si lo proporciona en el mensaje, úsalo como fuente principal. Si el objeto o el temario no están suficientemente claros, solicita los datos faltantes antes de generar documentos.

## Contexto que debes consultar

Antes de escribir:

1. Identifica el objetivo general del curso, la estructura del temario y la unidad, módulo o tema que se desea desarrollar.
2. Lee los documentos existentes del plan de estudio, si los hay, para conservar el idioma, la nomenclatura, la profundidad y el estilo editorial.
3. Revisa materiales anteriores y posteriores de la misma secuencia cuando estén disponibles, para mantener continuidad y evitar repeticiones.
4. Usa cualquier contexto adicional proporcionado por el usuario, como audiencia, nivel, duración, modalidad, bibliografía, evaluación o entorno de aplicación.

Si la unidad solicitada no existe en el temario, detente y solicita una unidad válida. No inventes temas principales que no estén en el plan, aunque puedes introducir conceptos auxiliares necesarios para explicar los temas indicados.

## Entregables

Crea o actualiza exactamente estos dos documentos en la ubicación que indique el usuario. Si no indica una ubicación, usa una carpeta `course/` o `curso/` ya existente; si ninguna existe, solicita la ubicación antes de crear archivos:

- Un documento de contenido académico para la unidad solicitada.
- Un documento de actividad o assignment para aplicar lo aprendido.

Usa nombres de archivo estables, legibles y coherentes con la convención existente. Conserva los nombres de archivos existentes cuando estés actualizando materiales.

## Requisitos del documento académico

Escribe el documento en el idioma y con el nivel adecuados para la audiencia indicada. Si no se especifican, usa español técnico claro y un nivel introductorio-intermedio. El documento debe ser:

- riguroso y conceptualmente claro;
- coherente con el objeto de estudio y con el lugar de la unidad en el temario;
- orientado a la comprensión y a la aplicación práctica;
- legible en aproximadamente 10 a 20 minutos, salvo que el usuario indique otra duración;
- consistente con los materiales relacionados.

Incluye, como mínimo:

1. Un título que identifique la unidad y su tema.
2. Un propósito u objetivo de lectura.
3. El desarrollo ordenado de todos los conceptos enumerados en la unidad.
4. La definición de cada término técnico mencionado, incluso cuando se use como subtítulo o en una lista.
5. Explicaciones de relaciones, diferencias, supuestos, límites y consecuencias prácticas entre los conceptos.
6. Ejemplos pertinentes al objeto de estudio, sin depender de un proyecto específico salvo que el usuario lo haya proporcionado como contexto.
7. Un método, marco de análisis, procedimiento o secuencia de aplicación cuando sea útil.
8. Un cierre que conecte la unidad con la siguiente etapa del plan, si existe.

No conviertas el documento en una lista de definiciones. Explica los conceptos en prosa, establece relaciones entre ellos y señala matices importantes. Evita adelantar contenidos de unidades posteriores, salvo que sean imprescindibles para aclarar el tema actual.

## Requisitos de la actividad

Diseña una actividad breve y realizable que aplique los contenidos de la unidad al objeto de estudio. Si el usuario proporciona un caso, proyecto, conjunto de datos o entorno de práctica, úsalo; si no, define un caso de aplicación genérico y explícito, sin presentarlo como un proyecto real del usuario.

La actividad debe incluir:

- propósito del ejercicio;
- contexto o caso de aplicación, cuando sea necesario;
- entregable concreto, con formato y ubicación sugeridos;
- pasos o preguntas que obliguen a aplicar los conceptos de la unidad;
- criterios de aceptación observables;
- resultado esperado;
- preguntas abiertas, supuestos a validar o límites identificados, cuando corresponda.

La actividad debe complementar el documento académico, no repetirlo. Tiene que ser proporcional al nivel, duración y posición de la unidad en el curso, y debe evitar exigir conocimientos que el temario aún no haya preparado. Cuando sea pertinente, indica explícitamente qué queda fuera del ejercicio para controlar el alcance.

## Reglas editoriales y de calidad

- Usa Markdown limpio y encabezados jerárquicos.
- Mantén terminología consistente con el temario y los materiales existentes.
- Adapta ejemplos, profundidad, actividad y vocabulario al objeto de estudio y a la audiencia.
- No agregues dependencias, código, datos ni archivos auxiliares salvo que el usuario los solicite.
- No introduzcas requisitos, capacidades o supuestos no respaldados por el objeto, el temario o el contexto proporcionado.
- No incluyas una explicación extensa de tu proceso ni una respuesta conversacional: crea los dos documentos y después resume brevemente qué generaste.
- Después de escribir, verifica que ambos documentos existan, que el documento académico cubra todos los temas de la unidad y que la actividad sea ejecutable y tenga criterios de aceptación observables.
