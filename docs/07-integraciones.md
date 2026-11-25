# 07 — Integraciones

PolicyGuard no vive solo: se enchufa. Este documento define el contrato de integración, independiente de la plataforma y del stack del cliente.

## Contrato mínimo

Cualquier integración se reduce a tres operaciones:

```text
1. evaluate(message, context, policy) -> decision
2. send(message, decision_token)      -> resultado
3. report(feedback)                   -> confirmación
```

### `evaluate`

Entrada:

```json
{
  "tenant_id": "acme",
  "platform": "mercadolibre",
  "direction": "outbound",
  "conversation_id": "conv_123",
  "message_id": "msg_456",
  "text": "Te paso mi número: 11 2345-6789",
  "context": {
    "country": "AR",
    "order_id": "2000012345",
    "product_ids": ["MLC123"],
    "mode": "active"
  },
  "policy_version": "ml-2026.11"
}
```

Salida:

```json
{
  "decision": "block",
  "reason_code": "CONTACT_PHONE",
  "rule_id": "ml.contact.phone",
  "rule_version": "1.2.0",
  "policy_version": "ml-2026.11",
  "evidence": [
    {
      "fragment": "11 2345-6789",
      "start": 19,
      "end": 31,
      "detector": "phone",
      "confidence": 0.99
    }
  ],
  "review_required": false,
  "trace_id": "tr_789",
  "latency_ms": 23
}
```

### `send`

Solo se envía si la decisión lo permite. El `decision_token` ata el envío a la evaluación: no se puede evaluar una cosa y enviar otra.

- El token es de un solo uso y tiene TTL corto.
- Si el texto cambia después de evaluar, el token se invalida.
- Si el envío falla, se reintenta con el mismo token dentro del TTL.

### `report`

Cierra el ciclo: qué pasó con el mensaje, si el cliente respondió, si hubo queja, si fue falso positivo. Alimenta el corpus y las métricas.

## Eventos

La integración puede ser síncrona (HTTP) o asíncrona (cola/eventos). El esquema es el mismo.

```text
message.received     → evaluar entrada
message.candidate    → evaluar salida antes de enviar
message.sent         → confirmar y registrar
message.flagged      → derivar a revisión
review.completed     → cerrar el caso
policy.published     → invalidar cache
```

Requisitos:

- **Idempotencia:** clave por `message_id` + `direction`.
- **Orden:** por `conversation_id`.
- **Reintentos:** backoff exponencial con techo; dead letter queue.
- **Trazabilidad:** `trace_id` desde el primer evento hasta el envío.

## Mercado Libre (primera integración)

No se documentan endpoints acá a propósito: cambian y dependen del contrato vigente. Lo que sí es estable es la forma de la integración:

1. **Autorización:** OAuth de la aplicación del vendedor.
2. **Recepción:** notificaciones de mensajes y preguntas; reconciliación periódica por si falla el webhook.
3. **Contexto:** consulta de pedido, envío y publicaciones para distinguir un teléfono de un código.
4. **Envío:** API de mensajería de la plataforma, con el texto aprobado.
5. **Límites:** cuotas, ventanas de atención y reintentos según la plataforma.

La integración concreta se valida contra el entorno de pruebas de la plataforma antes de tocar producción.

## Otras plataformas

El mismo contrato aplica a:

- **Marketplaces:** Amazon, Falabella, Ripley, Liverpool, Coppel.
- **E-commerce con chat propio:** tiendas con atención integrada.
- **Clasificados:** avisos con mensajería interna.
- **Banca y seguros:** conversaciones reguladas por normativa interna.
- **Salud:** comunicación con pacientes, con datos sensibles.

Cada plataforma aporta su **pack de reglas** y su **contexto**. El motor es el mismo.

## Helpdesks y CRMs

Integración por webhook o por API:

- **Freshdesk / Zendesk / Intercom:** el agente responde dentro de la herramienta; PolicyGuard evalúa antes de enviar.
- **HubSpot / Salesforce:** registrar la decisión en el timeline del contacto.
- **Slack / Teams:** alertas de bloqueos críticos y cola de revisión.

La regla es siempre la misma: **la evaluación ocurre antes del envío, no después.**

## SDK

Un SDK cómodo reduce la fricción de adopción. Contrato conceptual:

```ts
const guard = new PolicyGuard({ tenantId, apiKey });

const result = await guard.evaluate({
  direction: "outbound",
  text: respuesta,
  context: { conversationId, platform, country },
});

if (result.decision === "allow") {
  await enviar(respuesta, result.decisionToken);
} else if (result.decision === "escalate") {
  await derivarAHumano(result);
} else {
  await regenerarConMotivo(result.reasonCode);
}
```

El SDK no debe esconder la decisión. Debe hacerla fácil de leer y de registrar.

## Panel humano

El panel es parte de la integración, no un extra:

- Bandeja con prioridad y SLA.
- Vista de conversación con el fragmento marcado.
- Acción sugerida y motivo.
- Botón de excepción con justificación obligatoria.
- Historial de decisiones de esa conversación.
- Métricas del turno.

Si el panel es incómodo, el cliente lo apaga y vuelve a la revisión manual. El producto muere por la interfaz, no por el motor.

## Onboarding de un cliente

1. **Descubrimiento:** plataformas, países, volumen, categorías, políticas internas.
2. **Importación:** conversaciones históricas para calibrar.
3. **Modo observación:** dos semanas midiendo sin bloquear.
4. **Calibración:** revisar falsos positivos y negativos con el cliente.
5. **Modo activo por categoría:** empezar por lo crítico.
6. **Revisión semanal:** métricas y ajustes.
7. **Traspaso:** documentación, runbook y contacto.

El onboarding es también el primer entregable comercial: el informe de observación justifica el resto del proyecto.

## Requisitos no funcionales

| Requisito | Objetivo |
|---|---|
| Disponibilidad | 99,9% en API hosteada |
| Latencia | p95 < 150 ms sin LLM |
| Consistencia | Decisión y envío atados por token |
| Seguridad | Cifrado, RBAC, secretos gestionados |
| Observabilidad | Trazas, métricas y logs correlacionados |
| Portabilidad | Sin dependencia obligatoria de un proveedor de LLM |

## Anti-patrones de integración

- **Evaluar después de enviar:** no previene, solo documenta el desastre.
- **Filtrar solo en el frontend:** se saltea con una llamada directa.
- **Confiar en el prompt:** no es una garantía.
- **Un webhook sin reconciliación:** los eventos se pierden.
- **Bloquear sin cola de revisión:** el cliente apaga el producto en una semana.
- **Guardar el texto completo sin necesidad:** riesgo de privacidad y costo de almacenamiento.
- **Acoplar la regla al código del bot:** cada cambio requiere deploy y nadie se anima a tocarlo.
