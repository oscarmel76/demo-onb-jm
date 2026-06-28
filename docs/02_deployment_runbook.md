# Runbook de despliegue — Unicredit prototype

Cómo se pone en producción el prototipo y cómo operarlo con seguridad.

## Producción

- **URL en vivo:** https://oscarmel76.github.io/demo-onb-jm/
- **Hosting:** GitHub Pages.
- **Pipeline:** `.github/workflows/deploy.yml` (GitHub Actions).

## Flujo automatizado

```
push a main  ->  job "validate"  ->  job "deploy"  ->  URL en vivo
                 (gate de calidad)   (publica Pages)
```

- **validate** (corre en push y en Pull Requests): verifica que existan los HTML requeridos, que no haya marcadores de conflicto de merge sin resolver, y un sanity check de HTML.
- **deploy** (corre solo en push a `main`, no en PR): sube el sitio estático y lo publica en Pages.
- Disparo manual disponible desde la pestaña **Actions** → "Deploy prototype to GitHub Pages" → *Run workflow* (`workflow_dispatch`).

## ⚠️ Paso manual pendiente (1 clic) — IMPORTANTE

Hoy el repositorio tiene **dos mecanismos de publicación conviviendo**: el pipeline de Actions (gateado) y el deploy "legacy" que GitHub hace directo desde la rama `main`. Ambos publican el mismo contenido, así que el sitio en vivo está correcto, **pero mientras el legacy siga activo el gate de validación NO bloquea de verdad**: aunque la validación falle, el legacy publicaría igual desde la rama.

Para que el pipeline gateado sea el único desplegador (y el gate bloquee de verdad), hay que cambiar la fuente de Pages una sola vez desde la UI (requiere permisos de admin del repo, que el token no tiene):

1. Ir a **Settings → Pages** del repositorio.
2. En **Build and deployment → Source**, seleccionar **GitHub Actions**.
3. Guardar.

Tras ese cambio, el deploy "legacy" desaparece y solo corre el pipeline de `deploy.yml`. Hacé un push de prueba para confirmar.

## Rollback

- **Revertir un cambio:** `git revert <commit>` y push a `main` (el pipeline republica la versión anterior).
- **Volver al deploy legacy** (si el pipeline diera problemas): Settings → Pages → Source → *Deploy from a branch* → `main` / `/ (root)`.

## Seguridad de credenciales

- El despliegue desde GitHub Actions usa el `GITHUB_TOKEN` interno (efímero por run); no requiere PAT.
- **Rotación de PAT:** cualquier Personal Access Token usado para operar el repo desde fuera (clonar/pushear manualmente) debe ser fine-grained, acotado a este repo, con expiración corta, y **revocarse** cuando deje de usarse. No commitear tokens al repositorio.

## Convención de trabajo

- Cambios de bajo riesgo: push directo a `main`.
- Cambios para mostrar a stakeholders / riesgo / cumplimiento: rama + Pull Request (corre la validación antes de publicar).
