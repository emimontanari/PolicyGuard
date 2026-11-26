# 09 — Roadmap

El roadmap está ordenado por **riesgo a validar**, no por features. Cada fase existe para responder una pregunta antes de invertir en la siguiente.

> Las tres líneas de producto (Core, Runtime y bot a medida) y su orden de apertura se definen en [13 — Productos y servicios](13-productos-y-servicios.md). Este roadmap ejecuta primero el **Core**; el Runtime aparece recién en la fase 5, cuando el mercado lo pida.

## Fase 0 — Concepto y corpus (semana 1)

**Pregunta:** ¿el problema existe y se puede describir con precisión?

Entregables:

- Esta documentación.
- 100 conversaciones sintéticas que cubran las categorías críticas.
- 20 conversaciones reales anonimizadas (propias o de un colaborador).
- Catálogo de ofuscación inicial.

Criterio de salida: el corpus permite medir al menos teléfono, email, link y datos de pago.

## Fase 1 — Motor mínimo y demo (semanas 2–3)

**Pregunta:** ¿el motor determinístico detecta lo que tiene que detectar, con evidencia?

> El alcance detallado de esta fase —qué entra, qué queda afuera, criterios de aceptación y plan de cinco días— está en [14 — MVP del Core](14-mvp-core.md).

Alcance:

- Normalizador (Unicode, homóglifos, separadores, leetspeak).
- Detectores: teléfono, email, URL, datos de pago.
- Motor de reglas con severidad y acciones.
- Registro de decisiones.
- Replay de conversaciones con salida legible.
- Tests: unitarios + fixtures por regla.

Fuera de alcance: panel, integración real, LLM, imágenes, audio, multitenancy.

Criterio de salida:

- Precisión ≥ 98% y recall ≥ 92% en las cuatro categorías.
- El replay demuestra un antes/después convincente.
- Cualquiera puede correr la demo con un comando.

## Fase 2 — Primera auditoría (semanas 4–6)

**Pregunta:** ¿alguien paga por el diagnóstico?

Alcance:

- Importación de un export real de conversaciones.
- Informe de auditoría con categorías, frecuencia y ejemplos anonimizados.
- Modo observación.
- Pack de reglas calibrado para ese cliente.

Criterio de salida: un cliente paga y recibe un informe que reconoce como valioso.

## Fase 3 — Implementación en producción (semanas 6–10)

**Pregunta:** ¿la capa funciona en el camino crítico sin romper la operación?

Alcance:

- Integración con una plataforma (Mercado Libre como primera).
- Evaluación de entrada y salida.
- Cola de revisión y panel mínimo.
- Modo activo por categoría.
- Métricas y alertas.
- Runbook de incidentes.

Criterio de salida:

- 100% de los mensajes salientes pasan por la capa.
- Falsos positivos < 1% en críticas.
- Ninguna fuga crítica en 30 días.
- El cliente puede operar sin ayuda diaria.

## Fase 4 — Productización (meses 3–6)

**Pregunta:** ¿el mismo trabajo se repite lo suficiente como para empaquetarlo?

Alcance:

- Multitenancy.
- Packs de reglas versionados por plataforma.
- Panel de políticas (editor, tests, publicación).
- API hosteada y SDK.
- Facturación y límites por plan.
- Documentación para autoservicio.

Criterio de salida:

- Dos clientes usan el mismo motor sin cambios de código.
- Onboarding en menos de un día.
- El soporte no consume más de X horas por cliente.

## Fase 5 — Escala (mes 6+)

**Pregunta:** ¿se puede crecer sin que el fundador esté en cada entrega?

Alcance:

- Canal de partners (agencias).
- White-label.
- Expansión a una segunda plataforma y a un segundo país.
- Contenido y comunidad.
- Certificación o aval de algún actor del ecosistema.

Criterio de salida: al menos la mitad de los ingresos no depende de venta directa.

## Lo que no está en el roadmap

- Construir un bot completo. Eso es el trabajo del cliente.
- Moderación general de contenido.
- Análisis de imágenes y audio antes de validar el texto.
- Un LLM propio.
- Una app móvil.
- Expansión a cinco plataformas antes de dominar una.

## Decisiones pendientes

| Decisión | Opciones | Cuándo |
|---|---|---|
| ¿Librería, sidecar o API? | Empezar por librería + demo; API después | Fase 1 |
| ¿Open source? | Núcleo abierto + packs pagos, o todo cerrado | Fase 3 |
| ¿Primera plataforma? | Mercado Libre (evidencia) u otra | Fase 3 |
| ¿Primer ICP? | Vendedor grande o agencia | Fase 2 |
| ¿Cómo se cobra la licencia? | Por tenant, por volumen o híbrido | Fase 4 |

## Métrica norte

**Mensajes evaluados sin fuga crítica y sin romper la operación.**

No es cantidad de reglas, ni clientes, ni ingresos. Es la promesa del producto, medida.
