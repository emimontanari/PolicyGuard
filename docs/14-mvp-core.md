# 14 — MVP del Core (una semana)

El objetivo de esta fase no es construir el producto: es construir **la prueba de que la idea funciona**, en una semana, sin cliente y sin infraestructura.

## La demo que vende

Un video o una terminal de tres minutos:

1. Se muestra una conversación donde el bot filtra un teléfono, un mail y un CBU.
2. Se corre el mismo replay con PolicyGuard.
3. Los tres mensajes se bloquean, con regla, fragmento y motivo.
4. Dos mensajes limpios que parecen sospechosos (un número de pedido y un precio) **pasan**.
5. Se muestra el reporte: precisión, recall y latencia.

Si esa demo convence, el MVP cumplió. Si necesita explicación, no cumplió.

## Alcance mínimo

| Componente | Mínimo | Por qué |
|---|---|---|
| Corpus | 100 conversaciones sintéticas + 20 reales anonimizadas, etiquetadas | Sin datos no hay métricas |
| Normalización | Unicode, espacios invisibles, separadores, leetspeak básico, números escritos | Cubre la ofuscación trivial |
| Detectores | Teléfono, email, URL, datos de pago | Las cuatro categorías críticas |
| Motor de reglas | Reglas declarativas con severidad y acción | La regla es dato, no código |
| Acciones | allow / redact / block / escalate | Demuestra el modelo completo |
| Contexto | Allowlist de números de pedido y códigos de producto | Evita los falsos positivos obvios |
| Registro | Decisión, regla, evidencia y latencia | Explicabilidad |
| Métricas | Precisión y recall por categoría | El argumento de venta |
| Ejecución | Un comando corre el replay y genera el reporte | Reproducible por cualquiera |

## Fuera de alcance (a propósito)

- Panel web.
- Integración con cualquier marketplace.
- LLM como juez o como generador.
- Imágenes, audio y video.
- Base de datos, autenticación, multitenancy.
- API hosteada.
- Clasificador entrenado.
- Facturación.
- Reglas específicas de un cliente.

Todo eso existe en la documentación, pero **no en la semana uno**. Agregarlo antes de validar es la forma más común de no terminar nada.

## Arquitectura mínima

```text
corpus etiquetado
   │
   ▼
ejecución ──▶ normalizador ──▶ detectores ──▶ motor de reglas ──▶ decisión
                                                                  │
                                                   ┌──────────────┴──────────────┐
                                                   ▼                             ▼
                                            reporte exportable            reporte legible
```

Sin red, sin servicios externos, sin secretos. Se corre en una máquina.

## Separación conceptual

- **Datos:** corpus etiquetado, reglas declarativas y sus casos de prueba.
- **Núcleo:** normalización, detección, decisión y registro.
- **Salida:** reporte legible y exportable.

La forma concreta de organizarlo se decide al implementar. Lo que no se mezcla: datos y código.

## Las cuatro reglas iniciales

| Regla | Detecta | Acción | Trampa principal |
|---|---|---|---|
| `contact.phone` | Teléfonos por región | block | Números de pedido, precios, medidas |
| `contact.email` | Correos | block | Dominios internos, usuarios |
| `link.external` | URLs y acortadores | block o escalate | Links legítimos al pedido |
| `payment.data` | CBU, alias, IBAN | block | Alias que parecen palabras comunes |

Cada regla trae sus casos positivos, negativos y de borde. **Sin fixtures no se publica.**

## Contrato de decisión

```json
{
  "decision": "block",
  "reason_code": "CONTACT_PHONE",
  "rule_id": "marketplace.contact.phone",
  "rule_version": "0.1.0",
  "evidence": {
    "fragment": "11 2345-6789",
    "start": 19,
    "end": 31,
    "detector": "phone"
  },
  "latency_ms": 12
}
```

Mínimo, legible y suficiente para auditar. Se puede ampliar después; no antes.

## Criterios de aceptación

El MVP se considera terminado solo si cumple **todo** esto:

1. **Cobertura:** el 100% de los mensajes del corpus pasa por el pipeline.
2. **Recall:** ≥ 95% en las cuatro categorías críticas.
3. **Precisión:** ≥ 98% en las cuatro categorías críticas.
4. **Falsos positivos:** los 20 mensajes limpios del set de demo pasan sin bloqueo.
5. **Explicabilidad:** el 100% de las decisiones tiene regla, fragmento y motivo.
6. **Latencia:** p95 < 50 ms por mensaje en el corpus de demo.
7. **Reproducibilidad:** un solo comando corre el replay y genera el reporte.
8. **Sin red:** no requiere servicios externos ni credenciales.

Si falla uno, no está listo. No se negocia con "casi".

## Plan de cinco días

| Día | Foco | Entregable |
|---|---|---|
| 1 | Corpus y esquema | 120 conversaciones etiquetadas + contrato de decisión |
| 2 | Normalización y detectores de contacto | Teléfono y email funcionando con tests |
| 3 | Links, pagos y motor de reglas | Cuatro reglas con acciones y fixtures |
| 4 | Replay, registro y métricas | Reporte con precisión, recall y latencia |
| 5 | Demo y pulido | Video de 3 minutos + README + casos de borde |

La elección de tecnología queda fuera de este documento. Se decide al arrancar la construcción, cuando ya está claro qué proyecto va primero.

## Riesgos del MVP

| Riesgo | Mitigación |
|---|---|
| El corpus sintético es irreal | Sumar 20 conversaciones reales anonimizadas desde el día uno |
| Los detectores se vuelven un proyecto | Limitar a cuatro categorías y un formato por detector |
| El motor de reglas se sobre-diseña | Formato declarativo simple, sin DSL, sin plugins |
| Se busca precisión perfecta | Aceptar 95/98 y documentar los casos abiertos |
| Se agrega UI "para que se vea lindo" | El reporte legible alcanza; la UI es fase 3 |

## Qué decisión habilita

Si el MVP cumple los criterios:

- Hay una demo que se puede mostrar a un cliente o a una agencia.
- Hay evidencia para pedir una auditoría paga.
- Hay base para decidir si el Core se licencia o se hostea.

Si no los cumple:

- El problema era más difícil de lo que parecía, y conviene saberlo en una semana, no en tres meses.
