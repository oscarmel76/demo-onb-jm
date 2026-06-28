# Unicredit · Financing prototype (Jamaica)

Prototipo navegable del flujo de onboarding de crédito de consumo en tienda (retail finance) para el mercado de Jamaica.

**Demo en vivo:** https://oscarmel76.github.io/demo-onb-jm/

## Contenido del repositorio

| Archivo | Descripción |
|---|---|
| `index.html` | Pantalla principal del prototipo (home / búsqueda de cliente). |
| `01-customer-search.html` | Búsqueda de cliente. |
| `simulation.html` | Simulación de financiamiento / oferta. |
| `docs/01_product_brief_unicredit.md` | Product Brief / visión del producto (contexto de negocio, alcance, estados, métricas). |
| `.github/workflows/deploy.yml` | Pipeline de puesta en producción (CI/CD a GitHub Pages). |

## Puesta en producción (CI/CD)

El despliegue está automatizado con **GitHub Actions → GitHub Pages**:

1. Haces `push` a `main` (o mergeas un PR).
2. El workflow corre el **gate de validación**: archivos requeridos presentes, sin marcadores de conflicto de merge, sanity HTML.
3. Si la validación pasa, **publica** automáticamente en la URL de la demo.

En **Pull Requests** solo corre la validación (no publica), para revisar antes de mergear. También se puede disparar manualmente desde la pestaña **Actions** (`workflow_dispatch`).

> Como es un sitio estático (HTML plano), no hay paso de build: el repo se publica tal cual.

## Flujo de trabajo recomendado

Para cambios de bajo riesgo, push directo a `main`. Para cambios que se mostrarán a stakeholders o a riesgo/cumplimiento, trabajar en una rama y abrir PR para que corra la validación antes de publicar.
