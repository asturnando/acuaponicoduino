# ADR-0008: Monorepo único en GitHub

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

El proyecto tiene 8 áreas con stacks distintos: firmware (C/C++), hardware (KiCad), backend (Python), web (TS/Next), mobile (TS/RN), infra (Docker/Bash), ai (Python), docs (mkdocs). Hay tres opciones de organización:

1. **Monorepo único**: todo en un repo.
2. **Poly-repo** (8 repos): uno por área.
3. **Híbrido**: monorepo para code (firmware/backend/web/mobile/ai) + repo separado para hardware.

### Trade-offs clave para este proyecto

- **Cambios cross-stack frecuentes**: modificar un topic MQTT toca firmware + backend + web + mobile a la vez. Con monorepo es **un commit atómico**; con poly-repo son 4 PRs sincronizadas.
- **Una sola persona maintainer**: el coste de gestionar 8 CIs, 8 dependabots, 8 release pipelines es prohibitivo.
- **CI selectivo por path**: GitHub Actions soporta `paths:` filters → solo builda lo que cambió. Sin perdida de eficiencia frente a poly-repo.
- **Documentación cruzada**: links relativos entre `firmware/README.md` y `docs/ARCHITECTURE.md` funcionan sin redirects/submodules.
- **Versionado**: distintos componentes pueden versionarse independientemente con tags `firmware-v2.0.0`, `backend-v2.0.0`, etc., aunque vivan en el mismo repo.

## Decision

**Monorepo único** en `github.com/asturnando/acuaponicoduino`, estructurado así:

```
acuaponicoduino/
├── firmware/       # C/C++ (ESP-IDF)
├── hardware/       # KiCad
├── backend/        # Python (FastAPI)
├── web/            # TypeScript (Next.js)
├── mobile/         # TypeScript (React Native + Expo)
├── infra/          # Docker, Caddy, Mosquitto, Grafana
├── ai/             # Python (Claude API + tool use)
├── scripts/        # bash + Python utilities
├── docs/           # mkdocs site
├── .github/        # Actions, issue templates
└── (meta-files)    # LICENSE, CHANGELOG, CONTRIBUTING, etc.
```

CI con path filters por workflow (`firmware-build.yml` solo se dispara si toca `firmware/**`, etc.).

## Consequences

### Positivas
- Cambios cross-stack en un PR atómico, mergeable de una vez.
- Una sola política de branch protection, una sola fuente de issues, un solo CHANGELOG.
- Onboarding de un colaborador: `git clone <una url>` y listo.
- Dependabot configurado por subdirectorio en un solo `dependabot.yml`.
- Releases coordinadas posibles (tag `v2.0.0` cubre todo).

### Negativas
- Repo más grande al clonar (~irrelevante hasta que hardware/ tenga muchos STEP files con LFS).
- Permisos finos por subdirectorio requieren CODEOWNERS — ya configurado.
- Si crece a 20+ contributors con áreas separadas, considerar split. Lejos de eso.

### Neutras
- Hardware en LFS (`*.step`, `*.stl`, `*.f3d`) — costes LFS de GitHub son benignos hasta varios GB.

### Riesgos
- Workflow YAML se vuelve complejo si crecen los path filters. Mitigación: `dorny/paths-filter` centraliza la lógica.

## Referencias

- [Why Google Stores Billions of Lines of Code in a Single Repository](https://research.google/pubs/pub45424/)
- [Dependabot config con multiple ecosystems](https://docs.github.com/en/code-security/dependabot/dependabot-version-updates/configuring-dependabot-version-updates)
- [GitHub Actions paths filter](https://docs.github.com/en/actions/using-workflows/triggering-a-workflow#using-filters)
