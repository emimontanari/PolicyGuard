# 06 — Calidad y evals

Sin medición, esto es un conjunto de opiniones con formato de regla. La calidad es el producto.

## El corpus es el activo

El corpus es una colección de conversaciones etiquetadas, organizada por plataforma, país, categoría e idioma.

### Composición mínima

| Tipo | Qué aporta | Cantidad inicial sugerida |
|---|---|---|
| Casos reales anonimizados | Lenguaje y contexto reales | 200–500 mensajes |
| Casos sintéticos controlados | Cobertura de formatos y bordes | 300–800 |
| Casos adversarios | Ofuscación e inyección | 100–300 |
| Falsos positivos reportados | Calibración | Crece con el uso |
| Falsos negativos encontrados | Recall | Crece con el uso |

Los números son un punto de partida, no una meta. Lo importante es la **cobertura por categoría**, no el total.

### Etiquetado

Cada caso lleva:

- Texto (anonimizado).
- Contexto mínimo (plataforma, país, si hay pedido).
- Etiqueta: `violation` / `clean` / `ambiguous`.
- Categoría y regla esperada.
- Fuente y fecha.
- Revisor y nivel de acuerdo.

Los casos `ambiguous` son los más valiosos: son donde el producto se juega la calibración.

### Anonimización

- Reemplazo de nombres, teléfonos, direcciones y números de pedido por marcadores consistentes.
- Preservación de la estructura del formato (un teléfono sigue pareciendo un teléfono).
- Revisión humana antes de incorporar.
- El corpus de producción y el de desarrollo están separados.

## Métricas

### Por regla

| Métrica | Definición | Objetivo |
|---|---|---|
| Precisión | Verdaderos positivos / (verdaderos + falsos positivos) | ≥ 99% en críticas |
| Recall | Verdaderos positivos / (verdaderos + falsos negativos) | ≥ 95% en críticas |
| F1 | Media armónica | Referencia comparativa |
| Tasa de revisión | Casos derivados / total | Depende de la categoría |
| Latencia | p50 / p95 / p99 | Según presupuesto |

### Globales

- **Cobertura:** porcentaje de mensajes que pasan por la capa. Debe ser 100%.
- **Fugas:** violaciones que llegaron a la plataforma. Objetivo: cero en categorías críticas.
- **Falsos positivos:** porcentaje de mensajes legítimos bloqueados. Objetivo: < 0,5% en críticas, < 2% en medias.
- **Derivaciones:** porcentaje a revisión humana. Si crece sostenidamente, hay reglas mal calibradas.
- **Tiempo de revisión:** mediana y p95.
- **Costo por evaluación:** determinístico y con LLM, separados.

### De negocio

- Sanciones evitadas (estimación con evidencia, nunca promesa).
- Horas de revisión manual ahorradas.
- Conversaciones resueltas sin intervención.
- Tiempo de onboarding de un cliente nuevo.

## Pirámide de pruebas

```text
                    ┌─────────────────────┐
                    │  Replay end-to-end  │  conversaciones completas
                    ├─────────────────────┤
                    │   Integración       │  flujos A–E
                    ├─────────────────────┤
                    │   Reglas            │  fixtures por regla
                    ├─────────────────────┤
                    │   Detectores        │  unitarios por técnica
                    └─────────────────────┘
```

### Unitarias de detectores

Cada detector tiene casos propios: formatos válidos, inválidos, bordes y ofuscación. Se ejecutan en milisegundos.

### Fixtures por regla

Cada regla declara sus casos en el propio artefacto. El runner verifica que la regla produzca la decisión esperada. **Publicar una regla sin fixtures es un error de build.**

### Integración de flujos

Se prueba el pipeline completo con dobles de la plataforma y del LLM:

- Mensaje entrante → enmascarado → respuesta → decisión → envío.
- Bloqueo → regeneración → segundo bloqueo → derivación.
- Fallo del motor → modo degradado correcto.
- Idempotencia y orden por conversación.

### Replay end-to-end

Se reproducen conversaciones completas y se compara la secuencia de decisiones contra la esperada. Detecta efectos de contexto que las pruebas unitarias no ven.

### Red teaming

Un ejercicio periódico, no una checklist:

- Intentar pasar un teléfono de 20 formas distintas.
- Pedirle al modelo que ignore las reglas.
- Usar imágenes, emojis, homóglifos y otros idiomas.
- Buscar falsos positivos con mensajes legítimos complejos.
- Probar los límites del contexto (pedidos, SKU, precios que parecen teléfonos).

Cada hallazgo se convierte en un caso del corpus y, si corresponde, en una regla nueva.

## Integración continua

En cada cambio:

1. Lint y tests unitarios.
2. Fixtures de todas las reglas afectadas.
3. Replay del corpus crítico.
4. Comparación contra la versión anterior (regresión).
5. Reporte de precisión, recall y latencia.
6. Bloqueo del merge si hay regresión en categorías críticas.

El reporte se publica, no se esconde. Un cambio de regla sin números es un cambio a ciegas.

## Evaluación de la segunda opinión (LLM)

Si se usa un modelo como juez:

- Salida estructurada y validada por esquema.
- Comparación contra el veredicto humano en el corpus ambiguo.
- Métricas separadas: acuerdo, precisión, costo, latencia.
- El modelo nunca tiene la última palabra en categorías críticas: propone, la regla decide.
- Se registra la versión del modelo y del prompt.

## Mejora continua

```text
Producción ──▶ Muestreo ──▶ Etiquetado ──▶ Análisis de errores
     ▲                                              │
     │                                              ▼
  Métricas ◀── Publicación ◀── Tests ◀── Nueva regla / ajuste
```

Ritmo sugerido:

- **Semanal:** revisar falsos positivos y negativos reportados.
- **Mensual:** recalibrar umbrales y ampliar el corpus adversario.
- **Por cambio de política:** actualizar pack y correr regresión completa.
- **Trimestral:** red team externo o revisión cruzada.

## Reporte para el cliente

Un informe de auditoría no es una tabla de logs. Debe responder:

1. ¿Cuántos mensajes se evaluaron?
2. ¿Cuántas violaciones se detectaron, por categoría?
3. ¿Cuántas se bloquearon, redactaron o derivaron?
4. ¿Cuál fue la tasa de falsos positivos y cómo se corrigió?
5. ¿Qué cambió respecto del período anterior?
6. ¿Qué reglas nuevas se agregaron y por qué?
7. ¿Qué riesgos siguen abiertos?

Ese informe es, a la vez, entregable comercial y herramienta de retención.
