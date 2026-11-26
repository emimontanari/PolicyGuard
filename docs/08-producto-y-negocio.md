# 08 — Producto y negocio

Este documento responde una sola pregunta: **¿cómo se convierte esto en dinero?**

La respuesta corta: primero como servicio, después como módulo, y solo al final como producto de suscripción. En ese orden, no al revés.

## Los dos negocios

| | Servicio | Producto |
|---|---|---|
| Qué se vende | Implementación, auditoría, acompañamiento | Licencia o API |
| Quién compra | El cliente del proyecto | El mismo cliente, o una agencia que lo replica |
| Cuándo cobra | Por proyecto, contra entrega | Por mes, por volumen o por tenant |
| Qué exige | Confianza, referencia, entrega | Distribución, soporte, marca |
| Riesgo | Bajo | Alto sin canales |

**Regla:** el servicio financia el producto. La pieza reutilizable sale de hacer el mismo trabajo tres veces, no de imaginarlo en una pizarra.

## Tres proyectos independientes

La misma capacidad se empaqueta de tres formas, para tres compradores distintos:

| Proyecto | Qué vende | Comprador | Cobro |
|---|---|---|---|
| [Core](../projects/01-core.md) | La capa de cumplimiento | Agencias y empresas que ya tienen bot | Licencia mensual o por volumen |
| [Runtime](../projects/02-runtime.md) | El chasis white-label para armar bots | Agencias sin equipo técnico; empresas sin bot | Licencia por tenant + implementación |
| [Bot a medida](../projects/03-bot-a-medida.md) | El servicio completo | Cliente final sin agencia | Proyecto + soporte |

**Regla de canal:** nunca vender el bot directo a un cliente de un partner. Si se compite con las agencias que revenden la capa, se pierde la distribución.

Cada proyecto puede construirse y venderse por separado. El detalle, el conflicto de canal y los precios hipótesis están en [13 — La familia PolicyGuard](13-productos-y-servicios.md).

## Compradores (ICP)

### ICP 1 — Vendedor grande de marketplace

- **Quién:** operación con volumen, varios empleados de atención, facturación concentrada en una plataforma.
- **Dolor:** una sanción le corta el ingreso; el equipo ya tuvo incidentes.
- **Qué compra:** auditoría primero, implementación después.
- **Cómo se convence:** con un informe de fugas sobre sus propias conversaciones, no con una demo genérica.
- **Precio:** alto por proyecto; la comparación es contra el costo de una cuenta cerrada.

### ICP 2 — Agencia que implementa asistentes

- **Quién:** vende bots a clientes finales; necesita entregar con garantías.
- **Dolor:** un incidente en un cliente le quema la reputación y le complica la renovación.
- **Qué compra:** el módulo, licencia por cliente o white-label.
- **Cómo se convence:** mostrando que el módulo le permite vender a clientes más grandes sin contratar un especialista.
- **Precio:** licencia mensual por tenant o por proyecto.

### ICP 3 — Agencia no-code

- **Quién:** arma automatizaciones con herramientas visuales; tiene el riesgo pero no la ingeniería.
- **Dolor:** no puede resolver el compliance y lo sabe.
- **Qué compra:** servicio gestionado o API simple, sin infraestructura propia.
- **Cómo se convence:** con fricción cero: "me pasás el texto, te digo si sale".
- **Precio:** suscripción baja, volumen alto, soporte mínimo.

### ICP 4 — Equipo interno de atención

- **Quién:** empresa con operación propia, sin agencia.
- **Dolor:** revisar a mano no escala y el error humano es caro.
- **Qué compra:** la capa + el panel de revisión.
- **Cómo se convence:** con el ahorro de horas y la reducción de incidentes.
- **Precio:** licencia mensual + implementación.

## La escalera de oferta

```text
1. Diagnóstico (gratis o simbólico)
   └─ 30 minutos + revisión de 20 conversaciones

2. Auditoría (precio fijo, 1 semana)
   └─ Informe de fugas + reglas + tests + plan

3. Implementación (por proyecto)
   └─ Capa integrada + panel + calibración

4. Módulo / licencia (mensual)
   └─ Uso continuo, packs de reglas, soporte

5. API (por volumen)
   └─ Self-service para agencias y equipos técnicos
```

Cada escalón existe para vender el siguiente. La auditoría es el caballo de Troya: entrega valor sin exigir confianza previa.

## Precios (hipótesis a validar)

Los siguientes valores son **hipótesis de trabajo**, no datos de mercado. Se validan con las primeras conversaciones.

| Oferta | Precio sugerido | Alcance |
|---|---|---|
| Diagnóstico | Gratis | 20 conversaciones, 30 minutos |
| Auditoría | USD 300–800 | 1 semana, informe + pack inicial + tests |
| Implementación | USD 1.500–5.000 | Integración + panel + calibración |
| Licencia por tenant | USD 49–149 / mes | 1 plataforma, packs, soporte por email |
| API por volumen | USD 0,001–0,01 por evaluación | Según costo real y margen |
| White-label para agencias | USD 199–499 / mes | Múltiples tenants, marca propia |

Criterios para fijar el precio:

- **Valor del riesgo evitado:** una cuenta con facturación mensual de USD 10.000 justifica ampliamente el gasto.
- **Costo de la alternativa:** revisión manual = horas-persona todos los meses.
- **Precio de la competencia:** no hay competencia directa; la comparación es con "no hacer nada" o con "hacerlo a mano".
- **Costo de adquisición:** si el canal es Workana, el precio debe cubrir la comisión y el tiempo de propuesta.

## Canales

| Canal | Para qué | Esfuerzo | Cuándo |
|---|---|---|---|
| Workana y plataformas de freelance | Primeros clientes, casos reales | Medio | Ahora |
| LinkedIn | Casos de estudio, posicionamiento | Constante | Desde el primer caso |
| GitHub + documentación | Credibilidad técnica y SEO | Bajo, continuo | Desde el inicio |
| Comunidades de vendedores | Acceso directo al ICP 1 | Medio | Cuando haya demo |
| Alianzas con agencias | Escala sin venta directa | Alto | Cuando haya módulo |
| Contenido (artículos, videos) | Autoridad en el nicho | Alto, lento | Mes 2 en adelante |

El orden importa: primero un caso real, después se cuenta. Contar antes de tener el caso es marketing vacío.

## Objeciones esperadas

| Objeción | Respuesta |
|---|---|
| "Ya le puse al prompt que no comparta datos" | Un prompt es una sugerencia; esto es una garantía testeada. Mostrar un caso donde el prompt falló. |
| "La plataforma ya filtra" | Filtra y sanciona después. Esto previene antes. |
| "Es caro" | Comparar con el costo de una cuenta restringida o de una persona revisando todos los mensajes. |
| "¿Y si bloquea mensajes válidos?" | Modo observación primero, métricas de falsos positivos, cola de revisión y calibración. |
| "Lo hacemos nosotros" | Que lo hagan; el costo es el corpus, los packs y el mantenimiento. Se puede ofrecer la auditoría igual. |
| "No quiero depender de un proveedor" | Se puede licenciar el motor o desplegarlo en su infraestructura. |

## Economía unitaria (bosquejo)

Para una licencia de USD 99/mes con 20.000 evaluaciones:

| Concepto | Costo estimado |
|---|---|
| Cómputo determinístico | < USD 5 |
| Base de datos y almacenamiento | USD 5–15 |
| LLM de segunda opinión (10% del tráfico) | USD 10–30 |
| Soporte | USD 15–30 |
| **Margen bruto** | **~40–60%** |

El margen mejora con volumen y con menos uso de LLM. La palanca no es subir el precio: es **resolver más casos con reglas y menos con modelos**.

## Foso competitivo

Lo que se copia fácil: el código del motor.
Lo que no se copia fácil:

1. **El corpus etiquetado** de conversaciones reales por plataforma y país.
2. **Los packs de reglas** mantenidos al día.
3. **La confianza** de haber evitado incidentes reales.
4. **El panel y el flujo de revisión** que el cliente ya adoptó.
5. **La marca** en un nicho específico.

Por eso el producto se construye en el servicio: cada cliente deja corpus, reglas y casos.

## Plan de 90 días

### Semanas 1–2 — Demo

- Corpus inicial: 100 conversaciones sintéticas + 20 reales anonimizadas.
- Motor mínimo: teléfono, email, link, datos de pago.
- Replay que muestra "antes pasaba, ahora se bloquea".
- Documentación pública en este repositorio.

### Semanas 3–4 — Primeras conversaciones

- 10 vendedores grandes contactados.
- 5 agencias contactadas.
- 3 diagnósticos agendados.
- Precio de auditoría definido y comunicado.

### Semanas 5–8 — Primera auditoría

- Un cliente paga la auditoría.
- Informe real con fugas detectadas.
- Pack de reglas calibrado con sus conversaciones.
- Caso de estudio anonimizado.

### Semanas 9–12 — Repetición

- Segunda y tercera auditoría.
- Comparar: qué reglas se repiten, qué se puede productizar.
- Decidir si la licencia tiene demanda real.
- Solo entonces, evaluar API self-service.

## Qué no hacer

- **No construir el SaaS antes de vender.** Sin distribución, es un gasto.
- **No prometer cumplimiento legal.** Se vende evidencia y reducción de riesgo, no una garantía absoluta.
- **No competir por precio.** El valor está en el riesgo, no en la hora.
- **No aceptar un cliente sin acceso a conversaciones.** Sin datos no hay calibración ni informe.
- **No mezclar el producto con el bot.** El bot es del cliente; la capa es tuya.
- **No abrir el código sin estrategia.** Open source puede ser distribución o puede ser regalar el foso; decidir con intención.
