# Core — Proyecto 01

> Parte de la familia PolicyGuard. **Puede construirse y venderse por separado.**

## En una frase

La capa que decide si un mensaje puede salir, con reglas, evidencia y métricas.

## Qué problema resuelve solo

Un asistente conversacional ya funciona, pero puede filtrar datos prohibidos por la plataforma: teléfonos, correos, links externos, datos de pago. Una sola fuga puede costar la cuenta del cliente.

El Core se interpone entre el asistente y el canal. No genera respuestas: **autoriza o bloquea las que ya existen.**

## A quién se le vende

- **Agencias** que construyen bots para varios clientes y necesitan blindar sus entregas.
- **Empresas** con un asistente interno en producción.
- **Plataformas** que atienden conversaciones reguladas.

No se le vende a quien todavía no tiene bot: ese es el comprador del Runtime.

## Qué entrega

- Evaluación de mensajes entrantes, salientes y de operadores humanos.
- Packs de reglas por plataforma, con severidad y acción.
- Decisión explicable: regla, fragmento y motivo.
- Registro de auditoría de cada decisión.
- Métricas de precisión, recall y falsos positivos.
- Panel de revisión humana, opcional según el cliente.

## Qué no entrega

- Generación de respuestas.
- Orquestación de herramientas.
- Memoria de conversación.
- La relación comercial con el cliente final.
- La operación diaria del asistente.

## Cómo vive solo

**Oferta de entrada:** una auditoría de una semana sobre conversaciones reales. Se entrega un informe de fugas, un pack de reglas calibrado y los tests.

**Oferta recurrente:** licencia de la capa, por tenant o por volumen, con actualizaciones de reglas.

**Primer cliente:** una agencia con bots en producción o una empresa con un asistente interno.

**No necesita** que existan el Runtime ni el Bot a medida. Puede ser el único proyecto de la familia que se construye.

**Mínimo vendible:** el alcance de la demo de una semana. Ver [14 — MVP del Core](../docs/14-mvp-core.md).

## Cómo vive en familia

- Es la **base del Runtime**: el chasis lo incluye.
- Puede usarse dentro del **Bot a medida**.
- Aporta el modelo de reglas, el corpus y la evidencia a los otros dos.
- Recibe de ellos casos reales y conversaciones que amplían el corpus.

## Señales de éxito

- Tres clientes usan el mismo motor sin cambios de código.
- El 100% de los mensajes salientes pasa por la capa.
- Cero fugas críticas en 30 días.
- Falsos positivos por debajo del 1% en categorías críticas.
- Un cliente renueva la licencia sin pedir una excepción permanente.

## Riesgos

- **Falsos positivos:** si bloquea mensajes legítimos, el cliente lo apaga. Se mitiga con modo observación y calibración.
- **Deriva de políticas:** la plataforma cambia las reglas y el producto queda viejo. Se mitiga con packs versionados y monitoreo.
- **Filtro nativo de la plataforma:** si la plataforma resuelve el 90%, el valor baja. Se mitiga con evidencia y auditoría, que la plataforma no da.
- **Competencia horizontal:** un proveedor grande puede lanzar algo similar. Se compite con corpus, packs y confianza.

## Preguntas abiertas

- ¿Se licencia, se hostea o se vende como servicio?
- ¿El núcleo es abierto o cerrado?
- ¿Cuál es la primera plataforma?
- ¿El panel de revisión es imprescindible en la primera versión?
- ¿Cómo se mide "sanción evitada" sin prometer imposibles?

## Lo que no se negocia

Determinación, versión, evidencia y tests. Si el Core pierde eso, deja de ser Core y se convierte en otro prompt.
