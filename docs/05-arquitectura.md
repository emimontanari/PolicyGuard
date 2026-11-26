# 05 — Arquitectura

Este documento describe la arquitectura lógica. Las tecnologías son ejemplos de implementación, no requisitos. El producto debe poder portarse sin reescribir el modelo de reglas ni el corpus.

## Principios

1. **El motor es stateless.** La evaluación es una función pura: mismo input + misma versión de política = mismo output.
2. **La política es dato.** Se carga desde un store versionado; no se compila dentro del binario.
3. **La latencia es un requisito.** El camino crítico no espera a un LLM salvo que la regla lo pida.
4. **Todo se puede reconstruir.** Cada decisión tiene traza, versión y evidencia.
5. **Falla seguro.** Si una dependencia no responde, la política define el comportamiento por severidad.
6. **Multi-tenant desde el diseño.** Aislamiento de datos, políticas y métricas.

## Componentes lógicos

```text
┌───────────────────────────────────────────────────────────────┐
│                        Plano de control                        │
│  Panel de políticas · Editor de reglas · Corpus · Métricas     │
└───────────────────────────────┬───────────────────────────────┘
                                │ publica versiones
┌───────────────────────────────▼───────────────────────────────┐
│                        Plano de evaluación                     │
│                                                                │
│  API / SDK ──▶ Orquestador ──▶ Normalizador ──▶ Detectores     │
│                                    │                │          │
│                                    ▼                ▼          │
│                              Contexto         Motor de reglas  │
│                                    │                │          │
│                                    └───────┬────────┘          │
│                                            ▼                   │
│                                     Decisión + evidencia       │
└───────────────────────────────┬───────────────────────────────┘
                                │
        ┌───────────────────────┼────────────────────────┐
        ▼                       ▼                        ▼
  Cola de revisión        Registro de auditoría     Métricas
        │                       │                        │
        ▼                       ▼                        ▼
   Panel humano          Almacenamiento append-only   Observabilidad
```

### API / SDK

El punto de entrada. Tres formas posibles, no excluyentes:

- **Librería:** se importa en el servicio del cliente. Menor latencia, menor aislamiento.
- **Servicio sidecar:** contenedor al lado del asistente. Balance entre control y aislamiento.
- **API hosteada:** el cliente llama por HTTP. Menor fricción, mayor dependencia.

### Orquestador

Coordina el pipeline: normaliza, resuelve contexto, ejecuta detectores, evalúa reglas, decide y registra. No contiene lógica de negocio; esa vive en las reglas.

### Normalizador

Convierte el texto a una forma canónica antes de detectar:

- Unicode NFKC, eliminación de caracteres de control y espacios invisibles.
- Mapeo de homóglifos y confusables.
- Expansión de leetspeak y números escritos.
- Colapso de separadores.
- Detección de idioma (para reglas que dependen de él).

### Detectores

Módulos independientes, cada uno con su contrato:

```text
detect(input: NormalizedText, context: ConversationContext) -> Detection[]
```

Un `Detection` incluye tipo, fragmento, posición, confianza y metadatos. Los detectores no deciden: informan.

### Contexto

Datos de la conversación que cambian la interpretación:

- Pedido, producto, SKU, estado de envío.
- País y moneda.
- Historial reciente.
- Reglas del tenant y excepciones vigentes.
- Modo de operación (observación, activo, sombra, emergencia).

Sin contexto, los falsos positivos se multiplican. Con demasiado contexto, la latencia y la privacidad sufren. El equilibrio es parte del diseño.

### Motor de reglas

Evalúa las reglas aplicables contra las detecciones y el contexto. Devuelve una decisión con evidencia. Debe ser determinístico y explicable.

### Cola de revisión

Recibe los casos derivados. Tiene prioridad, SLA y feedback. Es un componente de producto, no un detalle.

### Registro de auditoría

Append-only. Cada entrada es inmutable y verificable. Se puede exportar para auditorías externas.

## Topologías de despliegue

| Topología | Ventaja | Costo | Cuándo |
|---|---|---|---|
| **Librería embebida** | Latencia mínima, sin red | Actualizaciones por deploy del cliente | Un solo equipo, alto volumen |
| **Sidecar** | Aislamiento y lenguaje independiente | Infraestructura extra | Varios servicios, mismo cluster |
| **API hosteada** | Cero fricción, actualización central | Dependencia de red y proveedor | Agencias y clientes chicos |
| **Gateway de mensajería** | Intercepta todo el tráfico | Punto único de falla | Operación gestionada |
| **Worker asíncrono** | No bloquea la respuesta | No previene, solo detecta | Auditoría y observación |

La misma lógica de reglas debe funcionar en todas. Eso se logra separando **evaluación** de **entrega**.

## Modelo de datos conceptual

```text
tenants
  └── policies (versión, packs, overrides, modo)
        └── rules (id, versión, categoría, severidad, detector, acción)
              └── tests (positivos, negativos, borde)

conversations
  └── messages (dirección, texto cifrado, metadatos)

evaluations
  ├── input_ref
  ├── decision
  ├── detections[]
  ├── matched_rules[]
  ├── policy_version
  └── latency

reviews
  ├── evaluation_id
  ├── reviewer
  ├── action
  └── justification

events (append-only)
  └── todo cambio de política, regla, modo o excepción
```

Decisiones de datos:

- **Texto original:** cifrado en reposo, retención configurable, acceso auditado.
- **Evidencia:** se guarda el fragmento mínimo necesario, no el mensaje completo, cuando alcanza.
- **Métricas:** agregadas; nunca exponen contenido.
- **Aislamiento:** row-level security o esquema separado por tenant, según escala.

## Latencia

Presupuesto objetivo:

| Etapa | p95 |
|---|---|
| Normalización | < 10 ms |
| Detectores determinísticos | < 50 ms |
| Contexto (cacheado) | < 20 ms |
| Motor de reglas | < 20 ms |
| Registro asíncrono | 0 ms en el camino crítico |
| **Total determinístico** | **< 150 ms** |
| Segunda opinión con LLM | 0,5–1,5 s, solo si la regla lo exige |

Estrategias:

- Cache de políticas por versión (inmutable).
- Cache de contexto por conversación con TTL corto.
- Detectores caros solo para categorías que los necesitan.
- Escritura de auditoría en cola, con confirmación posterior.
- Timeout duro y decisión por defecto según severidad.

## Seguridad

- **Minimización:** no se envía al LLM lo que no necesita ver.
- **Cifrado:** en tránsito y en reposo; claves por entorno.
- **Acceso:** RBAC; el revisor ve solo lo que necesita; el auditor ve metadatos.
- **Secretos:** gestor dedicado, rotación, nunca en el repositorio.
- **Retención:** política explícita por tipo de dato y por tenant.
- **Integridad:** hash encadenado en el registro de auditoría.
- **Aislamiento:** un tenant no puede leer políticas, conversaciones ni métricas de otro.

## Escalabilidad

- Evaluación horizontal sin estado; el cuello es la base de datos.
- Políticas inmutables cacheadas en memoria.
- Particionado de auditoría por tiempo y tenant.
- Cola de revisión con prioridad; no es un bus genérico.
- Detectores opcionales como servicios separados para escalar por demanda.

## Modos de falla

| Falla | Comportamiento |
|---|---|
| Motor de reglas caído | Según severidad: crítico bloquea y deriva; medio permite y registra |
| Base de políticas no disponible | Última versión cacheada; si no hay, modo emergencia |
| LLM de segunda opinión caído | Se aplica la decisión determinística; se marca `degraded` |
| Cola de revisión saturada | Prioriza crítico; alerta; puede bloquear automáticamente |
| Plataforma de destino caída | Reintentos con backoff; no se pierde el registro |
| Reloj desincronizado | Toda decisión usa timestamp del servidor, no del cliente |

Ningún modo de falla puede terminar en "se envió sin evaluar" de forma silenciosa.

## Independencia tecnológica

La arquitectura describe responsabilidades, no herramientas. La implementación se decide recién cuando se elige qué proyecto construir primero.

Lo que debe sobrevivir a cualquier elección:

- **Contratos de datos:** los detectores, las reglas y las decisiones se describen como esquemas, no como interfaces de una herramienta.
- **Reglas declarativas:** una regla es un dato versionado, no código compilado.
- **Dependencias reemplazables:** ningún proveedor de modelos, almacenamiento o mensajería es obligatorio.
- **Portabilidad del corpus:** las conversaciones etiquetadas se exportan y se reutilizan en cualquier implementación.
- **Separación entre evaluación y entrega:** el motor decide; el transporte envía. Se pueden cambiar por separado.

Lo único que no se negocia: **determinación, versión, evidencia y tests.**
