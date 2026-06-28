# Unicredit — Product Brief / Visión (archivo de instrucciones del proyecto)

**Plataforma de onboarding de crédito de consumo en tienda — Jamaica**
Versión: 1.1 (supuestos validados + motor de decisión corregido sobre el prototipo)
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
- **Decisión** (`screen-decision`): **motor de reglas automáticas** con tres salidas — **Instant pre-approval** (pasa todas las reglas), **In analysis** (enrutada a revisión humana, *read-only* hasta que crédito devuelva decisión) y **Rejected** con motivo. La "In analysis" tiene dos sub-rutas: *manual underwriting* y *analysis* (ver §7.1). Estados de cliente/solicitud adicionales: **With observations** e **In arrears**.
- **Pipeline del CSR** (`screen-pipeline`) e **historial de ventas** (`screen-sales-history`): solicitudes en curso y enviadas, con tarjetas de aprobadas y rechazadas (con motivo de rechazo).
- **Perfil del cliente** (`screen-profile`).

---

## 6. Alcance del prototipo — In / Out (reconstruido sobre el prototipo real)

### Dentro de alcance (In)

- Flujo CSR-led completo: búsqueda → (registro si es nuevo) → vista de línea → carrito → simulación → review/KYC+PEP → envío a COSACS → decisión.
- Modelo de **línea pre-aprobada** con Spending Limit y MMI, y dos bolsas de producto (HP / Cash Loan).
- **Simulación** con promociones (rate/plazo/gracia), enganche, seguro CPI y cuota fija.
- **Verificación en el punto de envío:** TRN, contacto, dirección + proof of address, PEP (titular y parientes), autorización CSR.
- **Motor de decisión automática de tres salidas** (Instant pre-approval / In analysis / Rejected) contra reglas de límite, capacidad mensual, mora interna, rating y score.
- **Pipeline e historial** del CSR (aprobadas / rechazadas con motivo).
- Solicitud de **aumento de límite** (estado "pending review").

### Fuera de alcance (Out, v1)

- **Buró de crédito** (no se integra por ahora).
- **Autoservicio del cliente** (solo canal asistido en tienda).
- **Multimoneda** (solo JMD).
- Integración productiva real con COSACS / core (en el prototipo el envío y la decisión están simulados).
- Desembolso/fondeo real, y cobranza/administración post-originación.
- Cola/back-office del analista para resolver casos **In analysis** end-to-end (el prototipo muestra el estado y el banner, pero no la bandeja de trabajo completa del underwriter).

---

## 7. Máquina de estados de la solicitud (alineada al prototipo)

```
SEARCH ─► [CUSTOMER_FOUND | REGISTER_NEW ─► REGISTERED]
        ─► LINE_VIEW (línea pre-aprobada: limit, MMI, HP/Cash disponibles)
        ─► CART (ítems / monto a financiar / enganche)
        ─► SIMULATION (promoción, plazo, CPI, cuota fija)
        ─► REVIEW (TRN + contacto + dirección + proof of address + PEP + auth CSR)
        ─► SUBMIT_TO_COSACS
        ─► DECISION (motor de reglas): [INSTANT_PREAPPROVED | IN_ANALYSIS | REJECTED]
INSTANT_PREAPPROVED ─► (sale registrada → Sales history / Pipeline)
IN_ANALYSIS ─► (read-only hasta que crédito devuelve decisión) ─► [APPROVED | REJECTED]
REJECTED ─► (con motivo → Sales history: rejected applications)
```

Reglas de integridad que el prototipo debe sostener:

- No se llega a `SUBMIT_TO_COSACS` sin: proof of address adjunto, screening PEP resuelto y autorización del CSR.
- `INSTANT_PREAPPROVED` solo si la solicitud **pasa todas las reglas** del motor (§7.1).
- `IN_ANALYSIS` deja la solicitud **read-only** ("Credit application under review — read-only until credit returns a decision"); el CSR no puede modificarla hasta que vuelva la decisión.
- El producto (HP vs Cash Loan) **ramifica las reglas de cumplimiento** (Cash Loan = regulado BOJ).
- Concurrencia en tienda: una misma solicitud/carrito no debe poder operarse en paralelo desde dos sesiones (origen típico de inconsistencias).
- Una solicitud enviada es inmutable; un cambio abre una nueva.

### 7.1 Motor de decisión (reglas reales del prototipo)

La decisión NO es binaria. El motor evalúa la solicitud contra un conjunto de reglas; si **alguna** se dispara, la solicitud se enruta a revisión humana (**In analysis**), distinguiendo dos sub-rutas: *manual underwriting* y *analysis*. Si **ninguna** se dispara, hay **pre-aprobación instantánea**.

| Regla | Condición que enruta | Sub-ruta |
|---|---|---|
| Pre-approved customer | Cliente ya pre-aprobado | manual |
| Credit rating | Rating D, E, Ñ o Z | manual |
| Days of internal delinquency | Mora interna > 7 días | manual |
| Locked account | Cuenta bloqueada | manual |
| Internal delinquency balance | Saldo en mora > $500 | manual |
| Amount financed vs. maximum credit amount | Monto > $800,000 | manual |
| Installment vs. max monthly installment | Cuota sobre MMI (con tolerancia) | manual |
| Amount financed vs. spending limit available | Monto sobre límite disponible (con tolerancia) | manual |
| Credit scoring | Score ≤ 460 | analysis |

Resultado: `manual` si se dispara alguna regla manual; si no, `analysis` si se dispara la de score; si no, `instant`. Estados de cliente/solicitud relacionados que el prototipo ya maneja: **Pre-approved**, **In analysis**, **With observations**, **In arrears**, **Rejected**, **Active**.

**Decisión de producto abierta (§11):** ¿*manual underwriting* y *analysis* van a **colas/SLA distintas** del back-office, o se atienden como una sola bandeja "In analysis"? Hoy se distinguen en la lógica pero comparten estado visible.

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
| "In analysis" sin bandeja de analista completa | Casos enrutados quedan sin resolución operativa | Definir cola/SLA del underwriter (§7.1) |
| Concurrencia en tienda | Datos inconsistentes / doble crédito | Bloqueo de carrito/solicitud en uso |
| Scope creep | Prototipo interminable | Corte In/Out de §6 como referencia firme |

---

## 10. Roadmap tentativo

- **Fase 0 — Fundación (hecha):** brief v1.0, repo conectado, CI/CD a GitHub Pages, prototipo navegable revisado.
- **Fase 1 — Endurecer el flujo feliz:** consistencia de estados, gates de cumplimiento por producto, casos de borde (carrito duplicado, sesión perdida, exceso de límite).
- **Fase 2 — Decisión y back-office:** bandeja del analista para casos **In analysis** (manual / analysis), SLA, pipeline y motivos de rechazo accionables.
- **Fase 3 — Contratos de integración:** definición formal de la interfaz con COSACS (cliente, línea, submit, decisión) para handoff a ingeniería; preparar el hueco para buró a futuro.

---

## 11. Decisiones registradas y pendientes

**Validadas:** Jamaica / JMD sin multimoneda; canal solo asistido en tienda; originador = retailer financiero; productos HP (no regulado) y Cash Loan cuota fija (regulado BOJ); una línea de crédito con bolsas HP/Cash; sin buró por ahora; público = stakeholders + equipo de desarrollo; stack = HTML plano.

**Validado adicional:** la decisión es un **motor de reglas de tres salidas** (Instant pre-approval / In analysis / Rejected); "In analysis" es el estado de revisión humana (lo que en v1.0 nombré erróneamente como decisión pendiente). Estados de cliente: Pre-approved, In analysis, With observations, In arrears, Rejected, Active.

**Pendientes:** (a) ¿*manual underwriting* y *analysis* en colas/SLA distintas o bandeja única "In analysis"? (§7.1); (b) reglas de cumplimiento específicas del Cash Loan bajo BOJ a confirmar con Legal; (c) TTL/vigencia de una simulación/oferta antes de requerir recálculo.

---

## 12. Changelog

- **v1.1 (2026-06-27):** corrección del modelo de decisión — NO es binario. Documentado el **motor de reglas de tres salidas** (Instant pre-approval / In analysis / Rejected), las reglas reales (rating, mora, score ≤460, límite, MMI, monto máx.), las sub-rutas manual/analysis y los estados de cliente (With observations, In arrears). El estado "In analysis" es el de revisión humana señalado por Oscar.
- **v1.0 (2026-06-27):** validación de todos los supuestos con Oscar; revisión del prototipo publicado; reconstrucción de In/Out y máquina de estados sobre la realidad (línea pre-aprobada, COSACS, HP vs Cash Loan, sin buró).
- **v0.1:** borrador inicial con supuestos por validar.
