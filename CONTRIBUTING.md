# Cómo contribuir

> Aunque hoy seas el único maintainer, este documento define las convenciones para que en cualquier momento alguien pueda entrar y aportar sin fricción.

## Principios

1. **Local-first.** Todo debe funcionar sin internet. El cloud es opcional.
2. **Sin atajos** que comprometan auditoría futura (CE, GDPR, HACCP).
3. **Atomic commits** cross-stack: un cambio que afecta a firmware + backend + web va junto.
4. **Documentar el _por qué_, no el _qué_.** El código bien nombrado ya dice qué hace.

## Flujo

1. Asegúrate de tener un issue abierto para lo que vayas a tocar (excepto fixes triviales).
2. Crea una rama con prefijo:
   - `feat/` nueva funcionalidad
   - `fix/` corrección de bug
   - `docs/` solo documentación
   - `hw/` hardware (KiCad, BOM, carcasa)
   - `fw/` firmware
   - `chore/` mantenimiento, deps, CI
   - `refactor/` refactor sin cambio funcional
   - `test/` añadir o mejorar tests
3. Trabaja, commitea siguiendo Conventional Commits (ver abajo).
4. Push, abre PR contra `main`.
5. CI debe estar verde + al menos 1 review (si hay reviewers).
6. Squash & merge por defecto.
7. Marca la tarea correspondiente en `docs/ROADMAP.md` con `[x]` y describe brevemente en `CHANGELOG.md`.

## Conventional Commits

Formato: `<type>(<scope>): <subject>`

- **type**: `feat`, `fix`, `docs`, `chore`, `refactor`, `test`, `perf`, `style`, `ci`, `build`, `hw`, `fw`
- **scope** (opcional): `firmware`, `backend`, `web`, `mobile`, `hardware`, `infra`, `ai`, `docs`, etc.
- **subject**: imperativo, minúscula inicial, sin punto final, ≤ 72 chars

Ejemplos:

```
feat(firmware): añadir driver de DS18B20 con error handling
fix(backend): corregir scope de org_id en query de telemetría
docs(adr): ADR-009 cierra licencia AGPLv3
hw(pcb): mover RL5-RL8 fuera del rango JTAG
```

Para breaking changes, añade `!` después del scope: `feat(api)!: cambiar formato de /devices/{id}/telemetry`.

## Setup local

```bash
# Pre-commit hooks
pip install pre-commit
pre-commit install

# Cuando trabajes en backend
cd backend && uv sync   # o poetry install

# Cuando trabajes en web
cd web && pnpm install

# Cuando trabajes en firmware
cd firmware && pio run
```

## Definition of Done

Una tarea se cierra solo si:

- [ ] Código mergeado en `main`
- [ ] Tests asociados pasan en CI
- [ ] Documentación actualizada
- [ ] Si introduce config nueva, `.env.example` actualizado
- [ ] Sin TODO/FIXME sin issue asociado
- [ ] La tarea correspondiente en `docs/ROADMAP.md` marcada `[x]`

## Reportar bugs

Usa los templates de issue. Mínimo: pasos para reproducir, comportamiento esperado, comportamiento real, versión (firmware + cloud), entorno.

## Discusiones

Para decisiones arquitecturales abre una **ADR** en `docs/adr/` antes de implementar. Plantilla en `docs/adr/template.md`.
