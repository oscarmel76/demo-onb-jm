# Unicredit — Product Brief / Visión (archivo de instrucciones del proyecto)

**Plataforma de onboarding de crédito de consumo en tienda — Jamaica**
Versión: 1.0 (supuestos validados con Oscar + revisión del prototipo publicado)
Owner de producto: Oscar Melgar
Última actualización: 2026-06-27
Demo en vivo revisada: https://oscarmel76.github.io/demo-onb-jm/

---

## 0. Cómo leer este documento

Este es el archivo de instrucciones fundacional del proyecto: define qué es Unicredit, para quién, con qué alcance y con qué reglas de negocio. Vive versionado dentro del repositorio (`/docs`) y es la referencia contra la que se construye el prototipo.

La versión 1.0 ya no contiene supuestos abiertos: todo lo que antes estaba marcado como hipótesis fue validado por Oscar o confirmado revisando el prototipo real. Donde queda una decisión pendiente, se marca como **PENDIENTE**.

---

## 1. El problema

Unicredit no resuelve "dar crédito a quien no lo tiene". Resuelve **convertir una línea de crédito pre-aprobada en una venta financiada en tienda, en minutos y sin papel**.

El cliente típico es un cliente existente del retailer que ya tiene una **línea de crédito pre-aprobada** (administrada en COSACS, el sistema retail legacy). Cuando quiere financiar un bien (Hired Purchase) o tomar un préstamo en efectivo (Cash Loan) contra esa línea, el proceso en sucursal hoy es lento y fragmentado:

- El CSR no ve de forma unificada el estado de la línea (límite disponible, capacidad mensual, mora, rating).
- La simulación de cuota, plazo, promoción y seguro se hace fuera del flujo de venta.
- La verificación de identidad y cumplimiento (TRN, dirección, PEP) se captura tarde o en paralelo, no como parte de la decisión.
- La decisión "sí/no" no es inmediata, y la venta se enfría.

✅ **Problema central (validado por Oscar):** el cuello de botella prioritario es el *time-to-decision* en sucursal, no el back-office de cobranza. El foco del prototipo queda en la experiencia de tienda (CSR-led) y en acortar el camino de la intención de compra a la decisión de crédito.

---

## 2. Visión

Unicredit es la capa de onboarding que convierte la línea de crédito pre-aprobada de un cliente en una decisión de financiamiento accionable en minutos, con identidad y cumplimiento verificados, simulación clara y solicitud lista para enviar a COSACS — sin sacar al cliente del mostrador.

En una frase: **"Del mostrador a la decisión de crédito, sin papel y sin esperas."**

---

## 3. Segmento y mercado (validado)

- **País:** Jamaica.
- **Moneda:** JMD únicamente. **No hay multimoneda.** ✅
- **Canal:** exclusivamente **asistido en tienda (CSR-led)**. El prototipo no contempla autoservicio del cliente. ✅
- **Figura del originador:** **retailer financiero** (un retailer que otorga crédito propio). ✅

**Usuarios del producto:**

1. **CSR / Vendedor de tienda** — usuario principal del prototipo. Opera todo el flujo: búsqueda, registro, simulación, verificación y envío.
2. **Cliente final** — cliente existente del retailer con línea pre-aprobada; en menor medida, cliente nuevo que se registra en tienda.
3. **Analista / supervisor** — revisa pipeline y solicitudes; gestiona casos como solicitudes de aumento de límite (estado "pending review").

**Público del prototipo (validado):** sirve para mostrar a stakeholders **y** como artefacto vivo de trabajo con el **equipo de desarrollo** (referencia funcional de lo que se construye). El brief y el prototipo deben poder leerse desde ambos lentes.

---

## 4. Productos de crédito y marco regulatorio (validado)

El cliente recibe **una línea de crédito**, y dentro de ella se pueden originar dos productos:

| Producto | Qué es | Regulación (según Oscar) |
|---|---|---|
| **Hired Purchase (HP)** | Financiamiento de bienes en tienda, cuota fija. | **No regulado.** |
| **Cash Loan** | Préstamo en efectivo de **cuota fija** contra la línea. | **Regulado por el Bank of Jamaica (BOJ).** |

**Implicación de diseño crítica:** HP y Cash Loan **no son el mismo flujo regulatorio**. El producto seleccionado debe condicionar los gates de cumplimiento: un Cash Loan (regulado BOJ) puede requerir validaciones/registros que un HP no exige. El prototipo debe tratar el producto como una variable que ramifica reglas, no como una simple etiqueta.

**Otros puntos de cumplimiento:**

- **Sin buró de crédito por el momento** (validado). La identidad y el historial se anclan en **TRN** y en **COSACS** (rating A–E y estado de mora propios del retailer), más verificación documental. Cuando se integre un buró, será una fase posterior.
- **AML / PEP:** el prototipo ya incluye screening PEP (titular y parientes) y autorización del CSR en el paso de confirmación. Aplica el marco POCA.
- **Proof of address obligatorio** como parte de la verificación.
- **Data Protection Act (2020):** tratamiento y minimización de datos del cliente.

> Nota: el tratamiento regulatorio de cada producto debe confirmarse con Legal/Cumplimiento local antes de producción real; aquí se documenta la regla de negocio aportada por el owner.

---

## 5. Estado actual del prototipo (base para el alcance)

Revisión del prototipo publicado (`index.html` + `simulation.html`, HTML plano). Pantallas y capacidades **ya construidas**:

- **Búsqueda de cliente** (`screen-empty`, `screen-data`, `screen-results`, `screen-noresult`): por documento/TRN, con resultados y estado "sin resultados".
- **Registro de cliente nuevo** (`screen-register-customer`, `screen-register-success`): tipo y número de documento, fecha de nacimiento, teléfono, teléfono fijo, email.
- **Home del cliente / vista de línea** (`screen-jada-home`): rating (A–E), estado de mora ("Up to date"), TRN, ID COSACS, **Spending limit** (Límite / Usado / Disponible, plazo máx. 48 meses), **Max Monthly Installment (MMI)** (capacidad mensual / comprometido / disponible), disponibilidad separada para **HP** y **Cash Loans**, solicitud de aumento de límite.
- **Carritos de compra** (`screen-carts`, `cart-modal`): ítems a financiar, monto de compra, enganche, monto a financiar.
- **Simulación de financiamiento** (`screen-simulation` / `simulation.html`): selección de **promoción** (fija rate, rango de plazo y periodo de gracia), selección de **plazo** (por defecto al plazo máximo), cuota mensual, primera cuota, plazo·tasa, gracia, **seguro CPI**, total financiado.
- **Review & confirm** (`review-view`): verificación de TRN, teléfono y teléfono fijo, **dirección + proof of address obligatorio**, **screening PEP** (titular y parientes con tipo de relación), autorización del CSR, y **envío de la solicitud a COSACS**.
- **Decisión** (`screen-decision`): resultado automático **Pre-approved / Rejected** con motivo, basado en reglas contra el límite disponible ("Within / Exceeds approved limit", "Automated decision rules", "Instant credit approval").
- **Pipeline del CSR** (`screen-pipeline`) e **historial de ventas** (`screen-sales-history`): solicitudes en curso y enviadas, con tarjetas de aprobadas y rechazadas (con motivo de rechazo).
- **Perfil del cliente** (`screen-profile`).

---

## 6. Alcance del prototipo — In / Out (reconstruido sobre el prototipo real)

### Dentro de alcance (In)

- Flujo CSR-led completo: búsqueda → (registro si es nuevo) → vista de línea → carrito → simulación → review/KYC+PEP → envío a COSACS → decisión.
- Modelo de **línea pre-aprobada** con Spending Limit y MMI, y dos bolsas de producto (HP / Cash Loan).
- **Simulación** con promociones (rate/plazo/gracia), enganche, seguro CPI y cuota fija.
- **Verificación en el punto de envío:** TRN, contacto, dirección + proof of address, PEP (titular y parientes), autorización CSR.
- **Decisión automática Pre-approved / Rejected** con motivo, contra el límite y la capacidad mensual.
- **Pipeline e historial** del CSR (aprobadas / rechazadas con motivo).
- Solicitud de **aumento de límite** (estado "pending review").

### Fuera de alcance (Out, v1)

- **Buró de crédito** (no se integra por ahora).
- **Autoservicio del cliente** (solo canal asistido en tienda).
- **Multimoneda** (solo JMD).
- Integración productiva real con COSACS / core (en el prototipo el envío y la decisión están simulados).
- Desembolso/fondeo real, y cobranza/administración post-originación.
- Estado **"Referido"** a analista en la decisión de crédito: **el modelo actual es binario (Pre-approved / Rejected) por reglas automáticas.** (Ver §6.1 — decisión a tomar.)

### 6.1 Decisión pendiente

**PENDIENTE — ¿se necesita un estado intermedio "Referido a analista"?** Hoy la decisión es binaria y automática. Si existen casos de zona gris (p. ej. monto que excede el límite pero el cliente tiene buen rating, o verificación documental incompleta) que ameriten revisión humana antes de rechazar, conviene introducir un estado `REFERRED`. Recomendación: definirlo en función de cuántos rechazos automáticos serían "recuperables" por un analista.

---

## 7. Máquina de estados de la solicitud (alineada al prototipo)

```
SEARCH ─► [CUSTOMER_FOUND | REGISTER_NEW ─► REGISTERED]
        ─► LINE_VIEW (línea pre-aprobada: limit, MMI, HP/Cash disponibles)
        ─► CART (ítems / monto a financiar / enganche)
        ─► SIMULATION (promoción, plazo, CPI, cuota fija)
        ─► REVIEW (TRN + contacto + dirección + proof of address + PEP + auth CSR)
        ─► SUBMIT_TO_COSACS
        ─► DECISION: [PRE_APPROVED | REJECTED(motivo)]
PRE_APPROVED ─► (sale registrada → Sales history / Pipeline)
REJECTED ─► (con motivo → Sales history: rejected applications)
```

Reglas de integridad que el prototipo debe sostener:

- No se llega a `SUBMIT_TO_COSACS` sin: proof of address adjunto, screening PEP resuelto y autorización del CSR.
- La decisión `PRE_APPROVED` exige que el monto a financiar esté **dentro del Spending Limit disponible** y dentro de la **capacidad mensual (MMI)**.
- El producto (HP vs Cash Loan) **ramifica las reglas de cumplimiento** (Cash Loan = regulado BOJ).
- Concurrencia en tienda: una misma solicitud/carrito no debe poder operarse en paralelo desde dos sesiones (origen típico de inconsistencias).
- Una solicitud enviada es inmutable; un cambio abre una nueva.

---

## 8. Métricas de éxito

**Métrica norte (North Star):** % de sesiones de venta en tienda que llegan a **solicitud enviada + pre-aprobada** usando la línea, en la misma visita.

Métricas de soporte: *time-to-decision* mediano (objetivo demostrable < 5 min en flujo feliz); tasa de abandono por etapa; % de pre-aprobaciones automáticas vs. rechazos; motivos de rechazo más frecuentes (para atacar fricción); tasa de errores operativos (duplicados, bloqueos).

---

## 9. Riesgos y dependencias

| Riesgo | Impacto | Mitigación en prototipo |
|---|---|---|
| Sincronización con COSACS (línea, mora, ID) | Decidir sobre datos desactualizados | Mostrar "Updated" / timestamp; modelar el submit/decision como contrato hacia COSACS |
| Cash Loan tratado igual que HP | Riesgo regulatorio (BOJ) | Ramificar reglas/gates por producto |
| Proof of address / PEP mal capturados | Riesgo de cumplimiento (POCA / KYC) | Gates obligatorios antes del submit |
| Decisión binaria sin "Referido" | Rechazos recuperables se pierden | Evaluar estado `REFERRED` (§6.1) |
| Concurrencia en tienda | Datos inconsistentes / doble crédito | Bloqueo de carrito/solicitud en uso |
| Scope creep | Prototipo interminable | Corte In/Out de §6 como referencia firme |

---

## 10. Roadmap tentativo

- **Fase 0 — Fundación (hecha):** brief v1.0, repo conectado, CI/CD a GitHub Pages, prototipo navegable revisado.
- **Fase 1 — Endurecer el flujo feliz:** consistencia de estados, gates de cumplimiento por producto, casos de borde (carrito duplicado, sesión perdida, exceso de límite).
- **Fase 2 — Decisión y back-office:** definir `REFERRED` si aplica, pipeline del analista, motivos de rechazo accionables.
- **Fase 3 — Contratos de integración:** definición formal de la interfaz con COSACS (cliente, línea, submit, decisión) para handoff a ingeniería; preparar el hueco para buró a futuro.

---

## 11. Decisiones registradas y pendientes

**Validadas:** Jamaica / JMD sin multimoneda; canal solo asistido en tienda; originador = retailer financiero; productos HP (no regulado) y Cash Loan cuota fija (regulado BOJ); una línea de crédito con bolsas HP/Cash; sin buró por ahora; público = stakeholders + equipo de desarrollo; stack = HTML plano.

**Pendientes:** (a) necesidad de estado `REFERRED` en la decisión (§6.1); (b) reglas de cumplimiento específicas del Cash Loan bajo BOJ a confirmar con Legal; (c) TTL/vigencia de una simulación/oferta antes de requerir recálculo.

---

## 12. Changelog

- **v1.0 (2026-06-27):** validación de todos los supuestos con Oscar; revisión del prototipo publicado; reconstrucción de In/Out y máquina de estados sobre la realidad (línea pre-aprobada, COSACS, decisión Pre-approved/Rejected, HP vs Cash Loan, sin buró).
- **v0.1:** borrador inicial con supuestos por validar.
