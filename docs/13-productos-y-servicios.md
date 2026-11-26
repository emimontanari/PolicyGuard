# 13 — La familia PolicyGuard

PolicyGuard es **una idea y tres proyectos**. La idea es la misma: decidir si un mensaje puede salir, con reglas, evidencia y métricas. Los proyectos son tres formas de llevarla al mercado.

Cada proyecto puede construirse y venderse **por separado**. Juntos forman una familia; ninguno depende de que los otros se vendan como productos independientes. El Runtime, por ejemplo, incluye el Core adentro: no necesita que el Core se venda aparte.

```text
                    ┌─────────────────────────┐
   Cliente final ──▶│    BOT A MEDIDA         │  servicio
                    └────────────┬────────────┘
                                 │ puede usar
                    ┌────────────▼────────────┐
   Agencia      ──▶│       RUNTIME           │  white-label
                    └────────────┬────────────┘
                                 │ incluye
                    ┌────────────▼────────────┐
   Empresa con  ──▶│        CORE             │  licencia
   bot propio       └─────────────────────────┘
```

## La idea compartida

En un canal regulado, un mensaje no se envía porque el modelo "quiera": se envía porque cumple las reglas. La capa que decide eso es determinística, explicable y medible. Esa es la semilla de los tres proyectos.

## Los tres proyectos

| Proyecto | Qué es | Comprador | Puede vivir solo | Documento |
|---|---|---|---|---|
| **Core** | La capa de cumplimiento | Quien ya tiene un bot | Sí, sin Runtime ni Bot | [01 — Core](../projects/01-core.md) |
| **Runtime** | El chasis para armar bots, con la capa incluida | Agencias y empresas sin bot | Sí, incluye el Core | [02 — Runtime](../projects/02-runtime.md) |
| **Bot a medida** | El servicio completo para un cliente final | Dueño de negocio sin agencia | Sí, con o sin la familia | [03 — Bot a medida](../projects/03-bot-a-medida.md) |

## Qué comparten

- **El modelo de reglas:** categorías, severidad, acciones y tests.
- **La evidencia:** cada decisión se explica y se audita.
- **Los principios:** determinación antes que criterio, test como contrato, falla segura.
- **El corpus:** conversaciones etiquetadas que mejoran con cada proyecto.
- **La marca y la reputación:** "el que verifica".
- **Las reglas de convivencia comercial.**

## Qué no comparten

- El comprador y el ciclo de venta.
- La oferta, el precio y el soporte.
- El alcance del mínimo viable.
- El nivel de responsabilidad que se asume.

Por eso cada uno tiene su propio documento: para que se pueda leer y vender sin depender de los demás.

## Cómo vive cada uno

| Proyecto | Vive solo si… | No necesita… | Primer cliente típico |
|---|---|---|---|
| **Core** | Ya existen bots en producción que pueden filtrar datos | Runtime ni Bot | Agencia con bots o empresa con asistente interno |
| **Runtime** | Hay agencias que quieren lanzar bots sin construir la plomería | Que el Core se venda como producto aparte | Agencia sin equipo técnico |
| **Bot a medida** | Hay un cliente final sin agencia que quiere el bot funcionando | Que exista el Core o el Runtime | Dueño de negocio o caso estratégico |

## Reglas de convivencia

1. **Un cliente de un partner es del partner.** No se le vende directo.
2. **Si el cliente final llega directo y no tiene agencia,** se puede hacer el bot a medida, pero primero se intenta derivarlo a un partner.
3. **El Core se vende a todos,** incluidos los competidores de una agencia: es horizontal.
4. **El Runtime se vende white-label.** La marca frente al cliente final es la de la agencia.
5. **La exclusividad se paga:** contrato y mínimo mensual.
6. **El Bot a medida usa la misma fuente de reglas,** no una copia paralela.

## Orden recomendado (no obligatorio)

La familia se puede abrir en cualquier orden. La recomendación es:

1. **Core**, porque es el más angosto y el que posiciona.
2. **Runtime**, cuando al menos tres agencias lo pidan.
3. **Bot a medida**, de forma oportunista, solo cuando no haya partner.

Abrir los tres el mismo mes es la forma más rápida de no terminar ninguno. Pero si el primero que aparece es un cliente de bot a medida, se toma: el orden es una guía, no una jaula.

## Precios (hipótesis a validar)

| Proyecto | Oferta | Precio sugerido |
|---|---|---|
| Core | Auditoría | USD 300–800 |
| Core | Licencia por tenant | USD 49–149 / mes |
| Core | White-label para agencias | USD 199–499 / mes |
| Runtime | Licencia por tenant | USD 99–299 / mes |
| Runtime | White-label | USD 499–1.500 / mes |
| Bot a medida | Proyecto | USD 1.500–5.000 |
| Cualquiera | Soporte mensual | USD 100–300 / mes |

Son hipótesis de trabajo, no una tarifa publicada. Se validan con las primeras conversaciones.

## Lo que hace que sea una familia y no tres negocios sueltos

- El mismo corpus y el mismo modelo de reglas.
- La misma promesa: evidencia, no humo.
- La misma reputación: cada proyecto alimenta a los otros.
- La misma regla de oro: **no competir con el canal**.

Si un proyecto se separa y empieza a contradecir estos puntos, deja de ser parte de la familia.
