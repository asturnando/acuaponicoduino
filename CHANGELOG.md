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
- Workflows de GitHub Actions (skeleton): ci, firmware-build, backend-test, web-build, docs-deploy, release
- Dependabot configurado para Python, Node y GitHub Actions
- mkdocs-material configurado, despliegue automático a GitHub Pages
- ADRs 001-010 documentando decisiones arquitecturales cerradas

### Changed
- ESP32-S3: variante final **N8R2** (descartado N16R8 por colisión PSRAM octal con GPIO 33-37)
- Debug: USB-Serial-JTAG nativo sobre USB-C (eliminado header SWD/JTAG dedicado)

---

> Este changelog se actualiza con cada PR mergeada. Las entradas en `[Unreleased]` se mueven a una sección versionada al hacer release.
