# 02 — Concepto

## Definición

PolicyGuard es una **capa de cumplimiento** que evalúa cada mensaje de una conversación regulada antes de que se envíe, y decide si se permite, se redacta, se bloquea o se deriva a revisión humana.

No genera respuestas. No conversa. No reemplaza al asistente. Se interpone.

## El modelo mental

Pensar en tres objetos cotidianos:

1. **El freno, no el motor.** El asistente acelera; PolicyGuard frena cuando hace falta. Si el freno intentara acelerar, sería un mal freno.
2. **La aduana, no el viaje.** Revisa qué entra y qué sale. No decide el destino del viajero.
3. **El cinturón de seguridad.** No evita el choque. Evita que el choque sea fatal.

La tentación de convertir esto en "un bot que responde bien" es el error de diseño más caro. **Cuanto más hace PolicyGuard, menos confiable es.**

## Los tres puntos de control

```text
                ┌─────────────────────────────────────────┐
   Cliente ───▶ │ 1. ENTRADA: enmascarar / detectar       │
                └───────────────────┬─────────────────────┘
                                    ▼
                          ┌──────────────────┐
                          │  Asistente LLM   │
                          └────────┬─────────┘
                                   ▼
                ┌─────────────────────────────────────────┐
                │ 2. SALIDA: validar / decidir / actuar   │
                └───────────────────┬─────────────────────┘
                                    ▼
                          ┌──────────────────┐
                          │   Plataforma     │
                          └──────────────────┘

   Operador humano ─────▶ 3. OPERADOR: mismo filtro que la salida
```

### 1. Entrada

Objetivo: que el modelo no vea ni repita lo que no debe.

- Detecta datos sensibles en el mensaje del cliente.
- Los reemplaza por marcadores (`[TELÉFONO]`, `[EMAIL]`, `[LINK]`).
- Guarda el original cifrado o truncado según política de retención.
- Registra el evento para auditoría.

No bloquea al cliente: el cliente puede escribir lo que quiera. Solo evita que ese dato se propague.

### 2. Salida

Objetivo: que nada prohibido llegue a la plataforma.

- Evalúa la respuesta del modelo (y opcionalmente la del operador).
- Aplica el pack de reglas de la plataforma y del tenant.
- Decide: permitir, redactar, bloquear o derivar.
- Si permite, firma la decisión (hash + versión de política) para auditoría.
- Si bloquea, puede pedir una regeneración con instrucciones o derivar a humano.

### 3. Operador

Objetivo: cubrir el error humano.

- Mismo motor, mismo pack de reglas.
- Interfaz que muestra el motivo antes de enviar, no después.
- Si el operador insiste en enviar, se requiere una justificación y queda registrada.

## Las cuatro acciones

| Acción | Cuándo | Efecto | Quién decide |
|---|---|---|---|
| **Permitir** | No hay coincidencias de riesgo alto | Se envía | Automático |
| **Redactar** | Coincidencia parcial, bajo riesgo | Se reemplaza el fragmento y se envía | Automático |
| **Bloquear** | Coincidencia crítica | No se envía; se genera alternativa o se deriva | Automático |
| **Derivar** | Ambigüedad o riesgo medio | Va a cola de revisión humana | Automático + humano |

La diferencia entre **bloquear** y **derivar** es de producto, no técnica: bloquear es para lo que nunca se puede enviar; derivar es para lo que una persona debe mirar.

## Severidad y falla segura

Cada regla tiene una severidad:

- **Crítica:** datos de contacto, pagos, links externos, coordinación fuera de la plataforma. Falla cerrado: si el detector no puede evaluar, se bloquea y deriva.
- **Alta:** promesas comerciales, condiciones, plazos. Falla cerrado con revisión.
- **Media:** lenguaje riesgoso, ambigüedad. Falla abierto con registro: se permite, pero se marca.
- **Baja:** estilo, tono. Solo se registra.

La regla de oro: **un falso positivo cuesta una revisión; un falso negativo cuesta la cuenta.**

## Explicabilidad

Toda decisión devuelve un objeto con:

- `decision`: allow | redact | block | escalate
- `reason_code`: identificador estable del motivo
- `rule_id` y `rule_version`
- `policy_version`
- `evidence`: fragmento detectado, posición, detector
- `confidence`: cuando aplica un detector probabilístico
- `review_required`: booleano
- `trace_id`: correlación con la conversación y el mensaje

Sin esto no hay auditoría, y sin auditoría no hay producto.

## Qué NO es PolicyGuard

> Nota de alcance: en este documento, "PolicyGuard" se refiere al **Core**, la capa de cumplimiento. Los proyectos Runtime (chasis del bot) y Bot a medida (servicio) se describen en [13 — La familia PolicyGuard](13-productos-y-servicios.md).

- **No es un chatbot.** No tiene personalidad ni genera conversación.
- **No es un orquestador de agentes.** No decide qué tool usar.
- **No es un filtro de moderación general.** No detecta odio, acoso o spam salvo que una regla lo pida.
- **No es un WAF.** No protege infraestructura.
- **No es un abogado.** Traduce políticas a reglas; la responsabilidad legal sigue siendo del cliente.
- **No es un prompt.** Es código, datos y tests.

## Tensiones de diseño

Todo el producto vive en cuatro tensiones. La documentación técnica las resuelve caso por caso.

### Precisión vs. recall

Un filtro agresivo bloquea mensajes legítimos (falso positivo) y rompe la operación. Un filtro laxo deja pasar fugas (falso negativo). La respuesta: **distinto umbral por severidad**, no un número mágico global.

### Determinación vs. cobertura

Las reglas cubren lo conocido. El lenguaje real es infinito. La respuesta: reglas para lo binario, clasificadores para lo difuso, LLM solo como segunda opinión sobre casos ambiguos, y siempre con revisión.

### Latencia vs. profundidad

Un detector de imágenes o un LLM juez agregan segundos. La respuesta: pipeline por etapas, con presupuesto de latencia y degradación explícita. Lo crítico se evalúa siempre; lo costoso, solo cuando hace falta.

### Autonomía vs. control

El cliente quiere que el asistente responda solo. El compliance quiere que nada salga sin revisión. La respuesta: **modo observación** primero (mide y no bloquea), luego **modo activo** por categoría. Se gana autonomía con evidencia.

## Nombre y metáfora

"Policy" es la regla de la plataforma. "Guard" es la capa que la hace cumplir. El nombre describe exactamente lo que hace: **guardar la política**, no interpretarla.
