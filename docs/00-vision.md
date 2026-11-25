# 00 — Visión

## Declaración

PolicyGuard existe para que una empresa pueda automatizar conversaciones reguladas sin poner en riesgo su canal de venta.

No vende inteligencia artificial. Vende **una garantía verificable**: lo que sale por el canal cumple las reglas de la plataforma, y hay evidencia de cada decisión.

## Misión

Convertir las políticas de comunicación de los marketplaces en reglas ejecutables, testeables y auditables, que cualquier asistente —propio, de un proveedor o armado a medida— pueda usar como capa de cumplimiento.

## Para quién existe

| Comprador | Dolor | Qué compra |
|---|---|---|
| Vendedor grande de marketplace | Una sanción le corta la facturación | Tranquilidad y evidencia de cumplimiento |
| Agencia que implementa asistentes | No sabe cómo blindar la entrega y teme perder al cliente | Un módulo reutilizable que la hace confiable |
| Agencia no-code | Tiene el mismo riesgo y cero ingeniería para resolverlo | Un servicio gestionado o una API simple |
| Equipo interno de atención | Revisar a mano no escala y el error humano es caro | Automatización con humano en el loop |

## Principios

1. **Determinación antes que criterio.** Lo binario se resuelve con reglas. El modelo entra solo donde la regla no alcanza.
2. **Toda decisión se explica.** Cada bloqueo tiene código de motivo, regla, versión de política y evidencia. Sin caja negra.
3. **El test es el contrato.** Una regla sin casos de prueba no existe. La calidad se mide con precisión y recall, no con opiniones.
4. **Falla cerrado en lo crítico.** Ante duda técnica en una categoría de riesgo alto, se bloquea y deriva a humano. Nunca se envía "por si acaso".
5. **El humano tiene la última palabra.** La cola de revisión no es un adorno: es parte del producto.
6. **Independiente del proveedor.** Funciona con cualquier LLM, cualquier orquestador y cualquier plataforma de mensajería.
7. **La política es un dato, no código.** Cambiar una regla no debería requerir un deploy.
8. **Privacidad por defecto.** Se procesa lo mínimo, se retiene lo necesario y se enmascara lo sensible.

## Criterios de éxito

### Técnicos

- **Precisión por regla:** ≥ 99% en las categorías críticas (datos de contacto, pagos, links externos).
- **Recall por regla:** ≥ 95% sobre el corpus etiquetado, con revisión manual de los falsos negativos.
- **Latencia:** p95 < 150 ms para la evaluación sin LLM; < 1,5 s cuando se usa un modelo como segunda opinión.
- **Disponibilidad:** 99,9% en modo API; degradación explícita y segura si falla una dependencia.
- **Cobertura:** el 100% de los mensajes salientes pasa por la capa, sin excepciones ni bypass silencioso.

### De producto

- Un cliente puede implementar la capa en menos de un día con la documentación.
- El panel muestra, en una pantalla, cuántos mensajes se bloquearon, por qué y cuánto se derivó a humano.
- Un auditor externo puede reconstruir una conversación y entender cada decisión.
- El costo variable por mensaje es despreciable frente al valor de evitar una sanción.

## Límites explícitos

- No garantiza el cumplimiento total de una plataforma: las políticas cambian y la responsabilidad final es del cliente.
- No detecta con certeza absoluta contenido en imágenes, audio o video en la primera versión.
- No decide sobre la calidad de la respuesta: eso es responsabilidad del asistente.
- No reemplaza el juicio legal ni las condiciones contractuales con el marketplace.

## Norte a 12 meses

Un producto que:
1. Tenga packs de reglas para al menos dos marketplaces relevantes.
2. Esté integrado en al menos tres asistentes en producción.
3. Publique métricas de eficacia sobre un corpus creciente.
4. Se venda como servicio de auditoría, como módulo de implementación y como API.
5. Sea la referencia hispanohablante del tema, con documentación y casos públicos.
