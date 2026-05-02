# Changelog

Formato basado en [Keep a Changelog](https://keepachangelog.com/es-ES/1.1.0/).
Versionado siguiendo [Semantic Versioning](https://semver.org/lang/es/).

## [Unreleased]

### Added
- Repositorio inicializado con `main` como rama principal
- `docs/ARCHITECTURE.md` v0.2 — pinout cerrado, variante MCU fijada en N8R2
- `docs/ROADMAP.md` v0.1 — checklist exhaustiva de M0 a M6
- LICENSE AGPLv3
- `.gitignore`, `.gitattributes`, `.editorconfig` para todos los stacks del monorepo
- `CONTRIBUTING.md`, `SECURITY.md`, `CODE_OF_CONDUCT.md`, `CODEOWNERS`
- Templates de issue (bug, feature, hardware) y de PR
- Pre-commit config con ruff, prettier, mypy, eslint, gitleaks
- Estructura de monorepo: `firmware/`, `hardware/`, `backend/`, `web/`, `mobile/`, `infra/`, `ai/`, `scripts/`
- Workflows de GitHub Actions: `ci.yml` (paths-filter + pre-commit + gitleaks), `firmware-build.yml`, `backend-test.yml` (con servicios timescaledb + redis), `web-build.yml`, `docs-deploy.yml` (mkdocs → Pages), `release.yml` (extrae notas del CHANGELOG)
- Dependabot configurado para `pip` (backend, ai), `npm` (web, mobile), `docker` y `github-actions`, con grupos lógicos por stack
- `mkdocs.yml` con tema Material, plugins (search, git-revision-date, glightbox, minify) y extensiones pymdownx (superfences/mermaid, tabbed, tasklist)
- Estructura `docs/` con secciones por área: hardware, firmware, backend, web, mobile, manual-usuario, manual-instalacion, api, troubleshooting, adr
- ADRs 001-010 documentando decisiones arquitecturales cerradas (MCU, framework firmware, backend, BD, web, mobile, broker MQTT, monorepo, licencia, hosting)

### Changed
- ESP32-S3: variante final **N8R2** (descartado N16R8 por colisión PSRAM octal con GPIO 33-37)
- Debug: USB-Serial-JTAG nativo sobre USB-C (eliminado header SWD/JTAG dedicado)

### Infrastructure
- Repo creado en [`asturnando/acuaponicoduino`](https://github.com/asturnando/acuaponicoduino) (público, AGPL-3.0)
- Branch protection en `main`: PR required, conversation resolution, no force pushes, no deletions
- Security: secret scanning + push protection + Dependabot alerts + Dependabot security updates + CodeQL (Python, JS/TS, GitHub Actions, queries `security-extended`)
- GitHub Pages source = workflow (`https://asturnando.github.io/acuaponicoduino/`)
- Milestones M0..M6 creados (8 milestones con due dates orientativas)
- Sistema de labels: `component:*` (8), `type:*` (7), `priority:*` (3), `status:*` (3)
- Settings repo: squash-only merge, delete branch on merge, discussions habilitadas, wiki off

---

> Este changelog se actualiza con cada PR mergeada. Las entradas en `[Unreleased]` se mueven a una sección versionada al hacer release.
