# Assignment Semana 5. Selección de comunicación para SkillHub

## Propósito

Elegir mecanismos de comunicación para flujos reales de SkillHub, justificando cuándo conviene esperar una respuesta y cuándo conviene procesar el trabajo después.

## Entregable

Crea `course/semana-05-communication-decisions.md` con una tabla y una justificación breve para estos tres flujos:

- consulta síncrona al sistema de empleados para obtener datos de una persona;
- notificación de una asignación o certificado;
- generación de un reporte inicial de capacitación.

Para cada flujo indica productor, consumidor, REST o RPC si corresponde, comunicación síncrona o asíncrona, uso de cola, Pub/Sub, evento o webhook cuando aplique, timeout o retención, reintentos, idempotencia y contrato mínimo.

## Criterios de aceptación

- Cada elección se relaciona con latencia, acoplamiento, resiliencia y volumen esperado.
- Distingue un comando de un evento y una cola de Pub/Sub.
- Identifica al menos una operación que requiera idempotencia y explica cómo evitar duplicados.
- Define qué ocurre ante timeout, consumidor indisponible o exceso de solicitudes.
- El contrato de cada flujo incluye datos, errores y compatibilidad básica.
- Incluye al menos tres supuestos o preguntas pendientes para validar.

## Fuera de alcance

No diseñes endpoints concretos, esquemas completos, proveedores, topología de despliegue ni tecnologías específicas. El resultado esperado es una decisión arquitectónica revisable que prepare el diseño de datos y contratos posteriores.