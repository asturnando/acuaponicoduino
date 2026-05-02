# Onboarding en GitHub

Este documento cubre los pasos que **no se pueden hacer desde el repo local**: requieren la UI de GitHub o `gh` (GitHub CLI) autenticado.

> Cuando termines todo lo de aquí, marca los checkboxes correspondientes en `ROADMAP.md` Fase 0.1 / 0.2 / 0.4.

---

## 1. Crear el repo remoto

### Opción A — desde el navegador (1 minuto)

1. Ir a <https://github.com/new>
2. Owner: `asturnando`
3. Repository name: `acuaponicoduino`
4. Description: `Sistema commercial-grade de control y monitorización para acuaponía. Refactor del TFG 2022.`
5. Visibility: **Public**
6. **NO** inicializar con README, .gitignore ni LICENSE (ya los tenemos en local).
7. Create repository.

### Opción B — desde la CLI con `gh` (recomendado)

Instalación:

```bash
# Arch / CachyOS
sudo pacman -S github-cli

# Debian / Ubuntu
sudo apt install gh

# macOS
brew install gh
```

Login y creación:

```bash
gh auth login                       # elegir GitHub.com → HTTPS → Login with browser
cd /ruta/al/repo/acuaponicoduino
gh repo create asturnando/acuaponicoduino \
  --public \
  --source=. \
  --remote=origin \
  --description "Sistema commercial-grade de control y monitorización para acuaponía. Refactor del TFG 2022."
```

---

## 2. Push inicial

```bash
git remote -v                       # verifica que origin existe
git push -u origin main
```

Si la opción A (navegador) y `git remote add` aún no se hizo:

```bash
git remote add origin git@github.com:asturnando/acuaponicoduino.git
# o, si prefieres HTTPS:
# git remote add origin https://github.com/asturnando/acuaponicoduino.git
git push -u origin main
```

---

## 3. Branch protection en `main`

UI: **Settings → Branches → Add branch ruleset** (o legacy: "Add rule").

Reglas mínimas:

- [x] Require a pull request before merging
- [x] Require status checks to pass before merging (cuando exista CI)
- [x] Require branches to be up to date before merging
- [x] Require conversation resolution before merging
- [x] Do not allow bypassing the above settings (incluso para administradores, opcional)
- [x] Restrict deletions
- [x] Block force pushes

Equivalente con `gh`:

```bash
gh api -X PUT repos/asturnando/acuaponicoduino/branches/main/protection \
  -F required_status_checks=null \
  -F enforce_admins=true \
  -F required_pull_request_reviews.required_approving_review_count=1 \
  -F restrictions=null
```

---

## 4. Activar features de seguridad

UI: **Settings → Code security and analysis**.

- [x] Dependency graph
- [x] Dependabot alerts
- [x] Dependabot security updates
- [x] Secret scanning
- [x] Push protection (bloquea push si detecta secretos)
- [x] CodeQL (en cuanto haya código real — auto-setup)

---

## 5. GitHub Pages (para mkdocs)

UI: **Settings → Pages**.

- Source: **GitHub Actions**
- Custom domain: vacío por ahora (cuando se pueda, `docs.acuaponicoduino.com`)

El workflow `.github/workflows/docs-deploy.yml` (pendiente, M0 Fase 0.3) hará el deploy automático.

---

## 6. Project board y milestones

UI: **Projects → New project** (board por columnas: Backlog / In Progress / Review / Done).

Milestones (UI: **Issues → Milestones**):

- M0 — Foundation
- M1 — Hardware
- M2 — Firmware
- M3 — Cloud Backend
- M3.5 — Infraestructura
- M4 — Frontend
- M5 — Diferenciadores
- M6 — Lanzamiento

Para importar las tareas de `ROADMAP.md` como issues etiquetadas:

```bash
# Script previsto: scripts/import-roadmap-as-issues.py (pendiente, M0 Fase 0.4)
```

---

## 7. Secrets del repo (cuando hagan falta)

UI: **Settings → Secrets and variables → Actions**.

Secretos que se irán necesitando:

| Nombre | Cuándo | Para qué |
|--------|--------|----------|
| `HETZNER_SSH_KEY` | M3.5 | Deploy a Hetzner |
| `DOCKER_REGISTRY_TOKEN` | M3.5 | Push a GHCR |
| `EAS_TOKEN` | M4 | EAS Build de mobile |
| `ANTHROPIC_API_KEY` | M5 | Asistente IA |

**Nunca** los pongas en `.env` commiteado. Repo-level secrets en GitHub Actions o vault local cifrado con `sops`.

---

## 8. Checklist final

- [ ] Repo creado en `asturnando/acuaponicoduino`
- [ ] `main` pusheada
- [ ] Branch protection activa
- [ ] Dependabot + secret scanning activos
- [ ] GitHub Pages configurado (Source: Actions)
- [ ] Milestones M0..M6 creados
- [ ] Project board creado y vinculado al repo

Cuando lo tengas todo, actualiza `docs/ROADMAP.md` Fase 0.1 / 0.4 marcando los `[x]`.
