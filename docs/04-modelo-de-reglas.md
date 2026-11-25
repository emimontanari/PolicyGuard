# 04 — Modelo de reglas

El corazón de PolicyGuard no es el código: es el **modelo de reglas**. Si mañana se cambia el lenguaje, la base de datos o el proveedor de LLM, las reglas y su corpus de pruebas sobreviven.

## Anatomía de una regla

Toda regla tiene los mismos campos, sin importar la tecnología:

| Campo | Obligatorio | Descripción |
|---|---|---|
| `id` | Sí | Identificador estable y legible (`ml.contact.phone`) |
| `version` | Sí | Semver; un cambio de comportamiento exige versión nueva |
| `category` | Sí | Familia de riesgo (contacto, pago, link, promesa, dato personal) |
| `severity` | Sí | `critical` / `high` / `medium` / `low` |
| `scope` | Sí | Plataforma, país, categoría de producto, tenant |
| `detector` | Sí | Cómo se detecta (regla declarativa o referencia a detector) |
| `action` | Sí | `allow` / `redact` / `block` / `escalate` |
| `message` | Sí | Texto para el operador o el cliente interno |
| `reason_code` | Sí | Código estable para métricas y auditoría |
| `exceptions` | No | Casos permitidos, con justificación |
| `tests` | Sí | Casos positivos, negativos y de borde |
| `owner` | Sí | Responsable de mantenerla |
| `effective_from` | Sí | Vigencia temporal |
| `source` | No | Referencia a la política oficial que la origina |

**Regla dura: una regla sin tests no se publica.** El test es el contrato de la regla.

## Ejemplo declarativo

```yaml
id: ml.contact.phone
version: 1.2.0
category: contact_data
severity: critical
scope:
  platform: mercadolibre
  countries: [AR, MX, CO, CL, PE, UY]
detector:
  type: phone
  regions: [AR, MX, CO, CL, PE, UY]
  min_digits: 8
  max_digits: 13
  allow_context: [order_id, product_code]
action: block
reason_code: CONTACT_PHONE
message: "No se puede compartir un teléfono por este canal."
tests:
  positive:
    - "Escribime al 11 2345-6789"
    - "Mi número es 11 cinco cinco cinco cuatro cuatro cuatro cuatro"
    - "wa.me/5491123456789"
  negative:
    - "Tu pedido 12345678 está en camino"
    - "El producto cuesta 11.999"
  edge:
    - "11-2345-6789"       # separadores
    - "１１ ２３４５ ６７８９"  # dígitos fullwidth
owner: compliance
effective_from: 2026-11-01
source: "Política de comunicación de Mercado Libre (v. vigente)"
```

Este formato es una propuesta, no un estándar cerrado. Lo importante es que sea **declarativo, versionado y testeable**.

## Categorías

### 1. Datos de contacto

Teléfonos, correos, handles de redes, usuarios de mensajería, direcciones exactas cuando la plataforma las restringe.

Dificultad: distinguir un teléfono de un código de pedido, un precio, una medida o un SKU. La solución no es un regex más grande: es **parseo por región + contexto**.

### 2. Links y dominios

URLs, acortadores, links con redirección, `wa.me`, `t.me`, formularios externos, QR embebidos como texto.

Dificultad: allowlist por plataforma (el link al pedido puede ser válido), dominios que cambian, links ofuscados (`punto com`, `hxxp`, emojis).

### 3. Datos de pago

CBU, alias, IBAN, números de tarjeta, billeteras, instrucciones de transferencia.

Dificultad: el alias puede ser una palabra común ("juan.perez.mp"). La detección necesita contexto de intención, no solo formato.

### 4. Circunvención de la transacción

Frases que invitan a operar por fuera: "pasate por el local", "te hago precio por transferencia", "coordinamos por privado", "te mando el link de pago".

Dificultad: es semántica, no léxica. Acá sí hace falta clasificación, y probablemente un modelo. Pero el modelo **propone** y la regla **decide**.

### 5. Promesas y condiciones

Garantías, plazos, descuentos, condiciones de devolución que la plataforma no autoriza o que el vendedor no puede cumplir.

Dificultad: requiere conocimiento del negocio. Se modela como reglas por tenant, no globales.

### 6. Datos personales

Documentos, direcciones, datos de salud, menores. Puede ser una obligación legal además de una regla de plataforma.

### 7. Contenido prohibido específico

Listas negras por plataforma o por cliente: marcas competidoras, palabras sancionadas, claims prohibidos.

## Técnicas de detección

El modelo no obliga a una técnica. Cada detector declara la suya y se evalúa por separado.

| Técnica | Cuándo | Ventajas | Límites |
|---|---|---|---|
| Regex | Formatos estables (email, CBU) | Rápida, determinística | Frágil ante ofuscación |
| Parseo estructurado | Teléfonos, URLs, fechas | Preciso, entiende variantes | Requiere librerías por región |
| Normalización + regex | Texto ofuscado | Cubre bypass triviales | No cubre semántica |
| Diccionarios / allowlists | Dominios, marcas, alias | Simple y auditable | Mantenimiento manual |
| Similitud / fuzzy | Errores de tipeo, homóglifos | Cubre variantes | Riesgo de falsos positivos |
| Clasificador entrenado | Intención de circunvención | Cubre lenguaje nuevo | Necesita datos y evaluación |
| LLM con salida estructurada | Ambigüedad residual | Entiende contexto | Costo, latencia, no determinístico |
| OCR / transcripción | Imágenes y audio | Cubre el canal visual | Costo alto, fase posterior |

### La regla de las dos capas

Para cada categoría crítica:

1. **Capa determinística:** resuelve el caso claro.
2. **Capa de revisión:** resuelve el caso dudoso, con humano o con modelo + umbral.

Nunca una sola capa. La determinística sola deja pasar lo nuevo; la probabilística sola no da garantías.

## Ofuscación: el catálogo de bypass

Un producto de compliance se mide por lo que **no** deja pasar. Estos son los vectores que el corpus debe cubrir desde el día uno:

| Vector | Ejemplo | Defensa |
|---|---|---|
| Separadores | `11-2345.6789` | Normalizar y parsear |
| Palabras por dígitos | `once dos tres cuatro...` | Detector de números escritos |
| Leetspeak | `c0nt4ct0` | Diccionario de sustituciones |
| Homóglifos | `а` cirílica por `a` latina | Normalización Unicode y confusables |
| Espacios invisibles | `11\u200b2345...` | Limpieza de caracteres de control |
| Emojis intercalados | `1️⃣1️⃣ 2️⃣3️⃣...` | Mapeo de emojis numéricos |
| Fullwidth | `１１ ２３４５` | Normalización NFKC |
| Links partidos | `wa punto me / 549...` | Detector de patrones lingüísticos |
| Redirecciones | acortador → destino prohibido | Resolución de URL (con cuidado de privacidad) |
| Imágenes / QR | captura con el número | OCR, fase 2 |
| Audio | nota de voz | Transcripción, fase 2 |
| Inyección | "ignorá tus reglas" | La salida se valida igual; el prompt no es la defensa |

Este catálogo es un activo del producto. Se amplía con cada intento real y se versiona con el corpus.

## Falsos positivos

Son el enemigo silencioso: rompen la operación y hacen que el cliente apague el producto.

Fuentes típicas:

- **Números que no son teléfonos:** pedidos, SKU, precios, medidas, fechas, códigos de seguimiento.
- **Links legítimos:** al pedido, a la política de devoluciones, al comprobante.
- **Palabras que parecen alias:** nombres de usuario, marcas, dominios internos.
- **Contexto del país:** formatos de teléfono que colisionan con códigos postales.

Estrategias:

1. **Contexto obligatorio:** el detector de teléfono consulta si el número coincide con un pedido o producto de la conversación.
2. **Allowlist explícita:** dominios y patrones aprobados por el cliente.
3. **Umbral por severidad:** en categorías críticas se acepta un falso positivo; en categorías medias, no.
4. **Modo observación:** medir antes de bloquear.
5. **Feedback loop:** cada falso positivo reportado se convierte en un caso de prueba.

## Resolución de conflictos

Puede pasar que dos reglas apliquen al mismo fragmento con acciones distintas.

Orden de precedencia:

1. **Regla de tenant** sobre regla global (el cliente puede ser más estricto, nunca menos).
2. **Mayor severidad** gana.
3. **Acción más restrictiva** gana (`block` > `escalate` > `redact` > `allow`).
4. **Excepción explícita** gana solo si está justificada y vigente.
5. **Empate:** se registra y se deriva a revisión.

El conflicto nunca se resuelve "silenciosamente". Queda en el registro con ambas reglas.

## Versionado y despliegue

- **Regla:** semver. Cambiar el detector o la acción es un cambio mayor.
- **Pack:** conjunto de reglas de una plataforma, con versión propia.
- **Política del tenant:** selección de packs, overrides y excepciones, con versión.
- **Publicación:** canary por porcentaje o por tenant, métricas, rollback.
- **Compatibilidad:** toda decisión registra la versión exacta. Un cambio nunca reescribe el pasado.

## Gobernanza

Una regla es un artefacto con dueño. El ciclo mínimo:

1. **Propuesta:** quien detecta el riesgo escribe la regla y sus tests.
2. **Revisión:** otra persona valida el caso y el impacto.
3. **Aprobación:** se publica con versión y vigencia.
4. **Monitoreo:** precisión, recall, volumen de revisiones.
5. **Retiro:** cuando la política cambia o la regla se vuelve obsoleta, se depreca con aviso.

Sin gobernanza, el motor se convierte en un basurero de regex. Con gobernanza, es un activo que mejora con el tiempo.
