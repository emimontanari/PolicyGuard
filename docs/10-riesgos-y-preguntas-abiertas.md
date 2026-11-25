# 10 — Riesgos y preguntas abiertas

Documentar lo que puede salir mal es parte del producto. Este documento no busca tranquilizar: busca decidir con los ojos abiertos.

## Riesgos técnicos

### 1. La ofuscación gana la carrera

Siempre va a existir una forma nueva de escribir un teléfono. **Mitigación:** no prometer cobertura total; prometer cobertura medida y un proceso para incorporar casos nuevos. El corpus y el ciclo de mejora son el producto, no la perfección.

### 2. Los falsos positivos rompen la operación

Un filtro demasiado agresivo bloquea mensajes legítimos y el cliente lo apaga. **Mitigación:** modo observación, umbral por severidad, contexto, allowlists y cola de revisión. Medir el costo de cada falso positivo.

### 3. Imágenes, audio y video

El dato prohibido puede estar en una captura o una nota de voz. **Mitigación:** declararlo fuera de alcance en la v1; agregar OCR/transcripción cuando el volumen lo justifique. No prometer lo que no se entrega.

### 4. Latencia

Cada detector agrega milisegundos. Un LLM juez agrega segundos. **Mitigación:** presupuesto de latencia, pipeline por etapas, cache, y modelos solo para lo ambiguo.

### 5. Dependencia de un proveedor de LLM

Si la segunda opinión depende de un modelo, cambia el precio, la latencia y el comportamiento. **Mitigación:** el núcleo es determinístico; el LLM es opcional y reemplazable.

### 6. Deriva de políticas

La plataforma cambia las reglas y el producto queda desactualizado. **Mitigación:** monitoreo de cambios, versionado, canary y comunicación proactiva al cliente.

## Riesgos de negocio

### 1. El comprador no percibe el riesgo hasta que le pasa

Muchos vendedores nunca tuvieron una sanción y no ven el valor. **Mitigación:** el informe de auditoría sobre sus propias conversaciones convierte un riesgo abstracto en un número concreto.

### 2. "Lo hacemos nosotros"

Un equipo interno puede construir un filtro básico. **Mitigación:** competir con el corpus, el mantenimiento y la evidencia, no con el código. Ofrecer la auditoría incluso a quien decide construir.

### 3. Dependencia de una sola plataforma

Si Mercado Libre cambia su ecosistema o cierra el acceso, el producto sufre. **Mitigación:** diseñar el motor agnóstico desde el día uno; la segunda plataforma es una validación, no una expansión.

### 4. Precio bajo del mercado

Workana está lleno de proyectos de USD 50–250. **Mitigación:** no competir ahí. La auditoría y la licencia se venden por valor de riesgo, no por hora.

### 5. Ciclo de venta largo

El comprador grande decide despacio. **Mitigación:** empezar por agencias y vendedores medianos; usar el servicio para financiar la espera.

### 6. El servicio consume todo el tiempo

Cada auditoría es artesanal. **Mitigación:** plantillas, importadores y un pack base reutilizable. Medir horas por entrega y bajarlas.

## Riesgos legales y de responsabilidad

- **No se puede garantizar el cumplimiento.** El producto reduce riesgo y aporta evidencia; la responsabilidad final es del cliente. Debe estar escrito en el contrato.
- **Datos personales.** El corpus contiene información sensible. Anonimización, cifrado, retención y acuerdos de tratamiento son obligatorios.
- **Términos de la plataforma.** El acceso a datos y el envío de mensajes deben respetar los términos vigentes. La integración no puede basarse en scraping no autorizado.
- **Publicidad engañosa.** No afirmar "cero sanciones" ni "cumplimiento 100%". Afirmar métricas medidas y un proceso.

## Preguntas abiertas

1. ¿La primera plataforma es Mercado Libre por la evidencia, o conviene otra por acceso?
2. ¿El primer comprador es un vendedor grande o una agencia? Cada uno cambia el producto.
3. ¿El motor se licencia, se hostea o se vende como servicio? La respuesta cambia la arquitectura.
4. ¿Conviene abrir el núcleo? ¿Qué se protege y qué se regala?
5. ¿Cuánto dura el onboarding real de un cliente con datos sucios?
6. ¿Qué pasa cuando el cliente ya tiene un filtro propio? ¿Se reemplaza o se complementa?
7. ¿Cómo se mide "sanción evitada" sin caer en una promesa imposible?
8. ¿El panel de revisión es imprescindible en la v1 o se puede operar por API?
9. ¿Qué nivel de latencia tolera una plataforma de mensajería en hora pico?
10. ¿Hay espacio para un partner de implementación en cada país?

## Supuestos a validar con interviews

| Supuesto | Cómo se valida |
|---|---|
| El riesgo de sanción es prioritario para el comprador | Preguntar por incidentes pasados, no por interés en IA |
| Pagaría por una auditoría antes de implementar | Ofrecerla y medir tasa de aceptación |
| El prompt no alcanza | Pedir ejemplos de fallas y revisar sus reglas actuales |
| La cola de revisión es aceptable | Medir volumen y disposición a revisar |
| El precio de USD 300–800 es razonable | Testear en tres conversaciones reales |
| La agencia es mejor canal que el vendedor | Comparar ciclo y ticket en ambos |

## Señales de que hay que parar o pivotar

- Después de 10 entrevistas, nadie reconoce el problema como prioritario.
- Los clientes prefieren revisar a mano antes que pagar.
- El costo de mantener reglas supera el valor percibido.
- La plataforma lanza un filtro nativo que resuelve el 90% del caso.
- El servicio no deja margen ni tiempo para productizar.

Ninguna de estas señales es fracaso: son información para decidir.
