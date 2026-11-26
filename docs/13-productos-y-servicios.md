# 13 — Productos y servicios

Una misma capacidad, tres formas de empaquetarla, para tres compradores distintos.

La tentación es vender "el bot completo" a todo el mundo. Eso destruye el canal: si competís con las agencias que deberían revenderte, dejás de tener distribución y volvés a vender de a un cliente por vez.

La arquitectura correcta es una escalera:

```text
   Dueño de negocio ──▶  BOT A MEDIDA        (servicio, caso especial)
                              │ usa
   Agencia          ──▶  RUNTIME            (white-label)
                              │ incluye
   Empresa con bot  ──▶  CORE               (API / SDK / licencia)
```

Cada línea se apoya en la anterior. Ninguna se saltea.

## Vista general

| Línea | Qué es | Comprador | Dolor que resuelve | Cobro | Canal |
|---|---|---|---|---|---|
| **Core** | Capa de cumplimiento | Agencias y empresas que ya tienen bot | "Mi bot puede filtrar datos y me sancionan" | Licencia mensual o por volumen | Directo y partners |
| **Runtime** | Chasis white-label para armar bots con la capa incluida | Agencias sin equipo técnico; empresas sin bot | "Necesito el bot funcionando, no solo el filtro" | Licencia por tenant + implementación | Partners |
| **Bot a medida** | Servicio completo para un cliente final | Dueño de negocio sin agencia | "Necesito que alguien me lo haga y me responda" | Proyecto + soporte | Directo, solo sin partner |

---

## 1. Core — la capa de cumplimiento

**Qué es:** el motor de reglas, detectores y evidencia. Se integra a un bot que ya existe.

**Incluye:**
- `evaluate` / `send` / `report`.
- Packs de reglas por plataforma.
- Registro de auditoría y evidencia.
- Panel de revisión opcional.
- Corpus y evals del cliente.

**No incluye:**
- Generación de respuestas.
- Orquestación de tools.
- Memoria de conversación.
- La relación comercial con el cliente final.

**Quién lo compra:**
- Agencia que ya construye bots y quiere blindar sus entregas.
- Empresa con un asistente interno en producción.
- Plataforma SaaS que atiende conversaciones reguladas.

**Por qué lo compra:** multiplica su riesgo por cada cliente, no quiere construir compliance y necesita diferenciarse en un mercado saturado.

**Cómo se cobra:** licencia mensual por tenant, por volumen de evaluaciones o white-label. La auditoría inicial se cobra aparte.

**Qué se promete:** evidencia, métricas y reducción de riesgo. **Nunca** garantía legal absoluta.

## 2. Runtime — el chasis del bot

**Qué es:** el motor completo para armar un bot: orquestación de LLM, tools, memoria, cola, webhooks, panel y guardrails integrados.

**Incluye:**
- Orquestador y máquina de estados de la conversación.
- Tools conectadas a la plataforma (pedidos, envíos, reclamos).
- Memoria y contexto por conversación.
- El Core adentro, sin costo adicional de integración.
- Multi-tenant y white-label.
- Panel de operación y revisión.

**No incluye:**
- La relación comercial con el cliente final: eso es de la agencia.
- El contenido, las políticas y la configuración del negocio.
- La operación diaria del cliente.

**Quién lo compra:**
- Agencia que quiere lanzar bots rápido sin contratar un equipo de platform.
- Empresa que quiere su propio asistente pero no quiere construir la infraestructura.
- Consultora que vende transformación digital y necesita un entregable técnico.

**Por qué lo compra:** time-to-market. El compliance y la plomería ya vienen resueltos; ellos ponen el cliente, la marca y el dominio.

**Cómo se cobra:** licencia por tenant + implementación; o contrato white-label anual con mínimo de tenants.

**Riesgo:** más soporte, más responsabilidad y más superficie de producto. No se abre antes de que el Core esté estable.

## 3. Bot a medida — el servicio completo

**Qué es:** el ciclo entero para un cliente final: descubrimiento, integración, bot, panel, puesta en producción y soporte.

**Incluye:**
- Todo el Runtime, configurado.
- Integración con la plataforma del cliente.
- Packs de reglas y calibración.
- Capacitación y runbook.

**Quién lo compra:** dueño de negocio que no tiene agencia ni quiere tenerla.

**Cuándo se ofrece:**
- El cliente llegó directo y no trabaja con ninguna agencia.
- Es un caso estratégico (el aviso de Mercado Libre, por ejemplo) que sirve de caso de estudio.
- No hay partner disponible en ese vertical o país.

**Cómo se cobra:** proyecto (el rango del aviso: más de USD 3.000) + soporte mensual.

**Riesgo:** compite con el canal, consume tiempo y no escala. Es una puerta de entrada, no el negocio.

---

## Reglas de convivencia (channel conflict)

1. **Un cliente de un partner es del partner.** No se le vende directo, ni se le ofrece el bot a medida.
2. **Si el cliente final llega directo y no tiene agencia,** se puede hacer el bot a medida, pero primero se intenta derivarlo a un partner.
3. **El Core se vende a todos,** incluidos los competidores de una agencia: es horizontal y no compite con nadie.
4. **El Runtime se vende white-label.** La marca frente al cliente final es la de la agencia, no la tuya.
5. **La exclusividad se paga.** Si una agencia pide exclusividad por vertical o país, se otorga con contrato y mínimo mensual.
6. **El bot a medida usa el Core licenciado,** no una copia paralela. Una sola fuente de verdad.

## Cómo se combinan

| Situación | Oferta | Por qué |
|---|---|---|
| Agencia con 15 bots en producción | **Core** | Ya tiene el bot; necesita el filtro |
| Agencia que quiere lanzar su línea de bots | **Runtime white-label** | No tiene equipo técnico; necesita el chasis |
| Empresa con asistente interno | **Core** | Ya tiene bot; quiere evidencia y protección |
| Clínica que pide un bot y no tiene agencia | **Bot a medida** o derivación | No hay partner; se resuelve o se canaliza |
| Agencia que quiere vender a clientes grandes | **Runtime + Core + auditoría** | Necesita garantías para subir de ticket |
| Empresa que tuvo un incidente | **Auditoría → Core** | Primero se mide el daño; después se previene |

## La auditoría como puerta de entrada

La auditoría de una semana sirve para las tres líneas:

- Al que ya tiene bot, le muestra las fugas y lo lleva al **Core**.
- Al que no tiene bot, le muestra el riesgo y lo lleva al **Runtime**.
- Al que necesita todo, lo lleva al **bot a medida**.

Es el mismo movimiento comercial en los tres casos: **primero se mide, después se vende.**

## Precios (hipótesis a validar)

| Línea | Precio sugerido | Unidad |
|---|---|---|
| Auditoría | USD 300–800 | Por cliente |
| Core | USD 49–149 / mes | Por tenant |
| Core white-label | USD 199–499 / mes | Por agencia, hasta N tenants |
| Runtime | USD 99–299 / mes | Por tenant, con implementación aparte |
| Runtime white-label | USD 499–1.500 / mes | Contrato anual, mínimo de tenants |
| Bot a medida | USD 1.500–5.000 | Proyecto, según alcance |
| Soporte | USD 100–300 / mes | Opcional |

Los valores son hipótesis de trabajo. Se validan con las primeras conversaciones, no se publican como tarifa.

## Secuencia recomendada

1. **Core + auditoría** (mes 1–2). Es el producto más angosto, más fácil de probar y el que posiciona.
2. **Runtime** (mes 3–6), solo cuando al menos tres agencias lo pidan explícitamente.
3. **Bot a medida** (oportunista), solo como caso estratégico o cuando no haya partner.

Abrir las tres líneas el mismo mes es la forma más rápida de no terminar ninguna.

## Qué no hacer

- Vender el bot directo a clientes de un partner.
- Ofrecer Runtime sin Core: el compliance es el diferencial, no un extra.
- Prometer cumplimiento legal absoluto en ninguna de las tres líneas.
- Construir el Runtime antes de que el Core tenga clientes.
- Aceptar un bot a medida que consume el tiempo que necesita el producto.
- Dar exclusividad gratis.
