# PolicyGuard

**Capa de cumplimiento determinística para asistentes conversacionales en marketplaces.**

> La IA conversacional resuelve el 80% del trabajo. El 20% restante —el que puede hacer que te cierren la cuenta— se resuelve con reglas, no con prompts.

---

## El problema en una escena

Un vendedor grande de Mercado Libre automatiza su postventa con un LLM. El asistente funciona bien: responde rápido, resuelve dudas, agenda devoluciones. Un día, un comprador pregunta cómo coordinar la entrega y el modelo responde:

> "Claro, escribime al 11 2345-6789 y lo vemos por privado."

El mensaje sale. La plataforma lo detecta. Sanción, cuenta restringida o cerrada. El negocio pierde el canal por el que factura todos los días.

El problema no es que el modelo sea malo. **El problema es que se le pidió criterio donde hacía falta una regla.**

## La tesis

Hay dos tipos de decisión en un asistente conversacional:

| Decisión | Naturaleza | Herramienta correcta |
|---|---|---|
| ¿Qué quiere el cliente y cómo responderlo? | Ambigüedad, lenguaje, contexto | Modelo de lenguaje |
| ¿Se puede enviar este mensaje? | Binaria, auditable, repetible | Reglas y tests |

PolicyGuard pone **determinación antes y después del modelo**. No reemplaza al asistente: lo cubre.

## Cómo funciona, en tres puntos de control

1. **Entrada:** enmascara datos sensibles que el cliente envía, para que el modelo no los repita ni los use.
2. **Salida:** valida la respuesta generada antes de enviarla. Bloquea, redacta o deriva a revisión humana.
3. **Operador:** aplica el mismo filtro a los mensajes escritos por personas. El error humano cuesta igual.

Toda decisión queda registrada con un código de motivo, la regla que la disparó y la versión de la política aplicada.

## Qué es y qué no es

**Es:**
- Una capa de cumplimiento para conversaciones reguladas.
- Un motor de reglas versionadas por plataforma, con tests y métricas.
- Una API, una librería o un sidecar que se enchufa a un asistente existente.
- Un producto de riesgo: se vende para evitar sanciones, no para "tener IA".

**No es:**
- Un bot ni un constructor de chatbots.
- Un modelo de lenguaje.
- Un filtro de moderación genérico (odio, violencia, spam).
- Un reemplazo del criterio humano en los casos dudosos.

## Estado

**Concepto documentado. Sin código todavía.** Este repositorio contiene la especificación completa: problema, concepto, modelo de reglas, arquitectura, calidad, integraciones, negocio y roadmap.

## Índice de documentación

| Documento | Qué contiene |
|---|---|
| [00 — Visión](docs/00-vision.md) | Para qué existe, principios, criterios de éxito |
| [01 — Problema](docs/01-problema.md) | El problema de negocio, los modos de falla, la evidencia |
| [02 — Concepto](docs/02-concepto.md) | Qué es PolicyGuard y qué no, modelo mental, decisiones de diseño |
| [03 — Cómo funciona](docs/03-como-funciona.md) | Flujos completos: entrada, salida, operador, auditoría, revisión |
| [04 — Modelo de reglas](docs/04-modelo-de-reglas.md) | Anatomía de una regla, categorías, detección, ofuscación, falsos positivos |
| [05 — Arquitectura](docs/05-arquitectura.md) | Componentes, topologías de despliegue, datos, latencia, seguridad |
| [06 — Calidad y evals](docs/06-calidad-y-evals.md) | Corpus, métricas, testing, red teaming, mejora continua |
| [07 — Integraciones](docs/07-integraciones.md) | Contrato de integración, marketplaces, helpdesks, SDK |
| [08 — Producto y negocio](docs/08-producto-y-negocio.md) | Compradores, oferta, precios, canales, plan de 90 días |
| [09 — Roadmap](docs/09-roadmap.md) | Fases, alcance del MVP, criterios de salida |
| [10 — Riesgos y preguntas abiertas](docs/10-riesgos-y-preguntas-abiertas.md) | Lo que puede salir mal y lo que falta validar |
| [11 — Evidencia de mercado](docs/11-evidencia-de-mercado.md) | Datos del relevamiento que originó la idea |
| [12 — Glosario](docs/12-glosario.md) | Términos técnicos y de negocio |

## Principio rector

> Si una regla se puede escribir, se escribe. Si no se puede escribir, se mide. El modelo opina solo donde no hay regla posible.

## Origen

La idea nace de un relevamiento de 129 avisos de Workana (noviembre de 2026). El proyecto mejor pagado y de mayor encaje para un perfil full-stack con IA era un asistente de postventa para Mercado Libre, con una frase textual del cliente:

> "El proyecto tiene como prioridad absoluta el cumplimiento de las políticas de Mercado Libre."

Ese requisito, repetido en otros avisos, es el producto.
