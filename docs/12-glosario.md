# 12 — Glosario

Términos usados en la documentación, en lenguaje llano.

## Producto

**Capa de cumplimiento**
Componente que se interpone entre un asistente y la plataforma para verificar que el mensaje cumpla las reglas antes de salir.

**Guardrail**
Barrera que limita lo que un sistema puede hacer o decir. En este contexto, la validación de entrada y salida.

**Modo observación**
La capa evalúa y registra, pero no bloquea. Se usa para medir antes de activar.

**Modo activo**
La capa bloquea, redacta o deriva según las reglas.

**Modo sombra**
Se evalúa una versión nueva de política en paralelo, comparando decisiones contra la versión vigente.

**Modo emergencia**
Se bloquea todo lo crítico y se deriva a humano ante un incidente o un cambio abrupto de la plataforma.

**Cola de revisión**
Lista priorizada de casos que un humano debe mirar, con contexto y SLA.

**Excepción**
Permiso explícito para enviar un mensaje que una regla bloquearía. Queda registrada con autor, motivo y vigencia.

## Reglas

**Regla**
Unidad mínima de cumplimiento: detecta una condición y define una acción.

**Pack de reglas**
Conjunto de reglas de una plataforma, país o vertical, con versión propia.

**Política**
Selección y configuración de packs para un tenant, más overrides y excepciones.

**Severidad**
Importancia de una regla: crítica, alta, media o baja. Define cómo falla el sistema.

**Detector**
Componente que identifica un patrón (teléfono, email, link, dato de pago) y devuelve evidencia. No decide.

**Acción**
Qué se hace con el mensaje: permitir, redactar, bloquear o derivar.

**Código de motivo**
Identificador estable que explica por qué se tomó una decisión (`CONTACT_PHONE`, `EXTERNAL_LINK`).

**Falso positivo**
Mensaje legítimo que la regla marca como violación.

**Falso negativo**
Violación que la regla deja pasar.

## Calidad

**Corpus**
Colección de conversaciones etiquetadas que se usa para medir y mejorar.

**Fixture**
Caso de prueba asociado a una regla: positivo, negativo o de borde.

**Precisión**
De lo que la regla marcó, qué proporción era realmente violación.

**Recall**
De las violaciones reales, qué proporción detectó la regla.

**Red teaming**
Ejercicio de intentar romper el sistema de forma adversarial para encontrar fallas.

**Replay**
Reproducir conversaciones completas y comparar las decisiones contra las esperadas.

**Regresión**
Cambio que empeora el comportamiento respecto de la versión anterior.

## Arquitectura

**Stateless**
El componente no guarda estado entre llamadas; el resultado depende solo de la entrada y la versión de política.

**Sidecar**
Proceso que corre al lado del servicio principal y se comunica localmente.

**Idempotencia**
Propiedad por la cual repetir la misma operación no produce efectos duplicados.

**Token de decisión**
Identificador de un solo uso que ata el envío de un mensaje a su evaluación previa.

**Append-only**
Registro donde solo se agregan entradas; no se modifican ni se borran.

**Multi-tenant**
Un mismo sistema atiende a varios clientes con datos y configuraciones aislados.

**Latencia p95**
El 95% de las operaciones tarda menos que ese valor.

**Degradación**
Comportamiento reducido y explícito cuando una dependencia falla.

## Negocio

**ICP**
Perfil de cliente ideal: quién tiene el problema, puede pagarlo y sabe que lo tiene.

**Auditoría**
Servicio de una semana que revisa conversaciones, detecta fugas y entrega un informe con reglas y tests.

**White-label**
Producto que una agencia ofrece con su propia marca.

**Margen bruto**
Ingreso menos costo directo de entregar el servicio.

**Foso competitivo**
Ventaja difícil de copiar: corpus, packs, confianza, marca.

**Ciclo de venta**
Tiempo desde el primer contacto hasta el cobro.

**Onboarding**
Proceso de incorporar un cliente nuevo: datos, calibración, modo observación y activación.
