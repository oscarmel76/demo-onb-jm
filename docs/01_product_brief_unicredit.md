# Unicredit — Product Brief / Visión

**Plataforma de onboarding de crédito de consumo — Jamaica**
Versión: 0.1 (borrador para validación)
Owner de producto: Oscar Melgar
Fecha: 2026-06-27
Estado: DRAFT — los supuestos marcados con 🔶 requieren validación de negocio/legal antes de fijarse.

---

## 0. Cómo leer este documento

Este brief es la pieza fundacional del prototipo. Vive dentro del repositorio (`/docs`) a propósito: es contexto versionado que alimenta tanto al equipo de producto como al pipeline. No es un documento muerto; cada release del prototipo debería poder rastrearse contra el alcance definido aquí.

Todo lo marcado con 🔶 es un supuesto operativo que asumí del contexto de retail finance en el Caribe. Confírmalos o corrígelos y los fijo como hechos.

---

## 1. El problema

En Jamaica, el acceso a crédito de consumo en punto de venta (retail finance) está limitado por procesos de originación lentos, manuales y fragmentados. El cliente típico que quiere financiar un electrodoméstico, un teléfono o un mueble en tienda enfrenta:

- Formularios en papel o capturas duplicadas entre el sistema de tienda y el core de crédito.
- Validación de identidad (TRN/NIDS) y verificación de ingresos desconectadas del flujo de decisión.
- Consulta a buró de crédito que no está integrada al momento de la venta, lo que obliga al cliente a "volver después".
- Tiempos de aprobación que matan la conversión: la decisión de compra se enfría si el "sí/no" no es inmediato.

Para el negocio, esto se traduce en abandono en tienda, costo operativo alto por expediente y exposición a riesgo por verificaciones inconsistentes (KYC/AML débil o tardío).

✅ **Problema central (validado por Oscar):** el cuello de botella prioritario es el *time-to-decision* en sucursal, no el back-office de cobranza. El foco del prototipo queda en la experiencia de tienda (CSR-led) y en acortar el camino de la intención de compra a la decisión de crédito.

---

## 2. Visión

Unicredit es la capa de onboarding de crédito que convierte una intención de compra en tienda en una decisión de crédito accionable en minutos, con identidad verificada, riesgo evaluado y contrato listo para firmar — sin sacar al cliente del mostrador.

En una frase: **"Del mostrador a la decisión de crédito, sin papel y sin esperas."**

---

## 3. Segmento y mercado

**País de arranque:** Jamaica (moneda JMD; consideraciones de doble moneda JMD/USD si aplica financiamiento de bienes importados 🔶).

**Usuarios primarios del producto:**

1. **CSR / Vendedor de tienda** — opera el frontend de punto de venta. No es experto en crédito; necesita un flujo guiado, a prueba de errores, rápido.
2. **Cliente final / solicitante** — quiere saber si califica, cuánto y en cuántas cuotas, con mínima fricción documental.
3. **Analista de crédito (backoffice)** — interviene solo en casos que el motor deja en zona gris (referidos), no en cada solicitud.

**Usuarios secundarios:** supervisor de tienda, oficial de cumplimiento (AML/KYC), administrador de producto/políticas, auditoría.

🔶 **Supuesto de canal:** el prototipo arranca por canal asistido en tienda (CSR-led), no autoservicio 100% digital del cliente. El autoservicio puede ser una fase posterior.

---

## 4. Contexto regulatorio y de riesgo (Jamaica)

Marco a tener presente en el diseño funcional (a confirmar con Legal/Cumplimiento local 🔶):

- **Bank of Jamaica (BOJ)** como regulador del sistema financiero; aplicabilidad depende de la figura legal del originador (banco, microfinanciera, retailer con licencia de crédito).
- **Credit Reporting Act (2010)** y reglamentos asociados: la consulta a buró requiere consentimiento explícito del cliente y solo a burós licenciados (p. ej. Creditinfo Jamaica, CRIF NM Credit Assurance). El consentimiento debe quedar registrado y auditable.
- **Proceeds of Crime Act (POCA)** y obligaciones AML/CFT: KYC, screening de listas (sanciones/PEP), y umbrales de reporte.
- **TRN (Tax Registration Number)** y **NIDS** como anclas de identidad. 🔶 Confirmar disponibilidad de validación electrónica de NIDS en el periodo del prototipo; si no hay API, se modela como verificación documental.
- **Data Protection Act (2020)**: tratamiento, minimización y retención de datos personales del solicitante.

**Implicación de diseño:** el consentimiento de buró y el screening AML no son "pasos administrativos", son *gates* del flujo. El estado de la solicitud no puede avanzar a decisión sin que ambos estén resueltos y sellados con timestamp + identidad del operador.

---

## 5. Alcance del prototipo (v1)

El prototipo NO es el sistema productivo completo. Es la demostración navegable del flujo de onboarding que valida el journey y sirve de contrato funcional con ingeniería.

### Dentro de alcance (In)

- Flujo CSR-led de originación: captura de solicitante → identidad → consentimiento buró → evaluación → decisión → oferta → contrato/firma.
- Estados de la solicitud visibles y consistentes (ver §6).
- Motor de decisión *simulado* (reglas/score mock) con resultados Aprobado / Referido / Rechazado.
- Pantalla de analista para resolver "Referidos".
- Manejo de los casos de borde críticos: solicitud duplicada, sesión perdida, doble envío, timeout de buró.

### Fuera de alcance (Out, v1)

- Integraciones reales con core bancario, buró y AML (se mockean con contratos de API definidos).
- Desembolso / fondeo real.
- Cobranza y administración del crédito post-originación.
- Autoservicio 100% del cliente sin CSR.

🔶 Validar este corte In/Out — es la decisión que más afecta esfuerzo y expectativas de stakeholders.

---

## 6. Modelo de estados de la solicitud (núcleo funcional)

El corazón del producto es la máquina de estados de la solicitud de crédito. Propuesta inicial:

```
DRAFT → IDENTITY_PENDING → BUREAU_CONSENT_PENDING → UNDER_EVALUATION
   → [APPROVED | REFERRED | DECLINED]
APPROVED → OFFER_PRESENTED → CONTRACT_PENDING → SIGNED → SUBMITTED_TO_CORE
REFERRED → ANALYST_REVIEW → [APPROVED | DECLINED]
```

Reglas de integridad que el prototipo debe hacer evidentes:

- Ningún estado avanza a `UNDER_EVALUATION` sin consentimiento de buró sellado.
- `APPROVED` con oferta tiene **vigencia** (expira); pasada la vigencia vuelve a requerir reevaluación. 🔶 Definir TTL de la oferta (sugerido 30 días).
- Una solicitud `SIGNED` es inmutable; cualquier cambio abre una nueva solicitud.
- Concurrencia: dos CSR no pueden operar la misma solicitud activa; el sistema bloquea o advierte (este es el origen típico de los "errores raros" en tienda).

---

## 7. Métricas de éxito

**Métrica norte (North Star):** tasa de solicitudes que llegan a `APPROVED + SIGNED` en la misma visita a tienda.

Métricas de soporte:

- *Time-to-decision* mediano (desde DRAFT a decisión). Objetivo de prototipo: demostrar < 5 min en flujo feliz. 🔶
- Tasa de abandono por etapa (dónde se cae el funnel).
- % de solicitudes auto-decididas por el motor vs. enviadas a Referido (mide carga del backoffice).
- Tasa de errores operativos en tienda (duplicados, bloqueos, reintentos).

---

## 8. Riesgos y dependencias

| Riesgo | Impacto | Mitigación en prototipo |
|---|---|---|
| Integración de buró no disponible a tiempo | Bloquea decisión real | Mock con contrato de API y manejo de timeout/caída |
| Validación NIDS/TRN electrónica inexistente | Identidad débil | Modelar verificación documental + flag de confianza |
| Concurrencia en tienda (mismo cliente, dos CSR) | Datos inconsistentes, doble crédito | Bloqueo de solicitud + estado de "en uso" |
| Consentimiento de buró mal capturado | Riesgo legal (Credit Reporting Act) | Gate obligatorio con sello auditable |
| Alcance creciente (scope creep) | Prototipo nunca termina | Corte In/Out de §5 fijado y firmado |

---

## 9. Roadmap tentativo (alto nivel)

- **Fase 0 — Fundación (actual):** brief, repo conectado, CI/CD a GitHub Pages, máquina de estados navegable.
- **Fase 1 — Flujo feliz:** originación CSR-led end-to-end con mocks.
- **Fase 2 — Casos de borde y analista:** Referidos, duplicados, concurrencia, timeouts.
- **Fase 3 — Contratos de integración:** definición formal de APIs (buró, core, AML) para handoff a ingeniería.

---

## 10. Preguntas abiertas para Oscar

1. ¿Figura legal del originador en Jamaica (banco / microfinanciera / retailer licenciado)? Define qué marco regulatorio aplica.
2. ¿El prototipo debe convencer a un stakeholder específico (inversión, riesgo, operaciones, regulador)? El público objetivo cambia el énfasis.
3. ¿Buró objetivo confirmado (Creditinfo Jamaica / CRIF NM)? ¿Hay sandbox de API disponible?
4. ¿Producto de crédito concreto del piloto (línea revolvente, cuotas fijas en tienda, tarjeta)? Cambia el flujo de oferta.
5. ¿El stack web "otro" del repo ya tiene una dirección (Vue/Angular/Svelte/HTML plano)? Lo confirmo al conectar, pero si ya lo sabes acelera.

---

*Documento vivo. Próxima iteración tras validar supuestos 🔶 y conectar el repositorio.*
