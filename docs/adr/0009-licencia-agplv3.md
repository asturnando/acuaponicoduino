# ADR-0009: Licencia GNU AGPLv3

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

El proyecto se declara en el README como "portfolio hoy, semilla comercial mañana", con los principios "sin atajos que comprometan auditoría futura (CE, GDPR, HACCP)" y "local-first". La licencia tiene que cuadrar con tres escenarios posibles a 1-3 años:

1. **Permanecer open source de comunidad**: contributors externos, releases periódicas, sin monetización.
2. **Comercializar como producto SaaS**: vender el cloud + soporte; la unidad embebida puede regalarse o subsidiarse.
3. **Open core / dual-licensing**: AGPL para uso comunitario, licencia comercial para empresas que quieran integrarlo sin obligaciones AGPL.

Una licencia tomada al principio condiciona los tres escenarios. **Relicenciar de permisiva (MIT/Apache) a copyleft (AGPL) es muy difícil** una vez aceptados PRs externos (cada contributor debería firmar un CLA). **Relicenciar de AGPL a permisiva es trivial** mientras seas el único copyright holder.

### Opciones evaluadas

| Licencia | Pros | Contras |
|----------|------|---------|
| MIT | Adopción máxima, sin fricción | Cualquiera puede coger el código y hacer un SaaS competidor sin devolver nada |
| Apache 2.0 | MIT + grant explícito de patentes | Igual que MIT en lo demás |
| **AGPLv3** | Si alguien ofrece el sistema modificado como servicio (SaaS), debe abrir su código. Habilita futuro **dual-licensing comercial**. Compatible con ser "buenos open source" para uso individual / comunidad / educación | Algunas empresas tienen política de no integrar AGPL. Coste asumido a cambio de protección |
| GPL-3.0 (no Affero) | Copyleft fuerte | Bug del SaaS: si la modificación solo se ofrece como servicio (sin distribución), GPL no obliga a abrir el código. AGPL cierra ese loophole — exactamente nuestro caso |
| Source-available (BSL, SSPL) | Más restrictivas que AGPL | No son OSI-approved. Reducen contribuciones de comunidad y compatibilidad con Linux distros |

## Decision

**Adoptamos GNU AGPLv3 para todo el monorepo** (`firmware/`, `hardware/`, `backend/`, `web/`, `mobile/`, `infra/`, `ai/`, `scripts/`, `docs/`).

- Texto canónico de gnu.org en `LICENSE` raíz.
- Cabeceras de archivo no obligatorias en MVP (ahorro de ruido). Se pueden añadir luego si fuera necesario.
- Dependencias: revisaremos compatibilidad de licencias en cada PR de dependencia (la mayoría MIT/Apache/BSD son compatibles).

### Por qué AGPL y no MIT

Si decido en M6 que el proyecto es 100% open source de comunidad: relicenciar de AGPL a MIT es un commit, mientras yo sea el único copyright holder. Al revés es imposible una vez hay PRs externos sin CLA. AGPL hoy preserva la opción de ambas direcciones; MIT hoy cierra la puerta a todo lo demás.

## Consequences

### Positivas
- Protección contra "fork → SaaS competidor sin devolver nada".
- Habilita **dual-licensing** futuro: ofrecer licencia comercial pagada a empresas que no puedan/quieran cumplir AGPL.
- Compatible con uso doméstico, educativo, investigación, open source — la inmensa mayoría de potenciales usuarios no están afectados.
- Señal clara al ecosistema de qué tipo de comunidad queremos construir.

### Negativas
- Algunas empresas tienen política contra AGPL. Pierden adopción potencial — coste aceptado.
- Contributors externos deben aceptar AGPL para sus PRs. Mitigación: claro en `CONTRIBUTING.md`. Si un día se hace dual-licensing, puede requerir CLA — decisión a tomar entonces.

### Neutras
- En firmware embebido, "ofrecer como servicio" es ambiguo. Mitigación: en la práctica, la mayoría de usos del firmware son privados; la AGPL impacta principalmente al backend SaaS.

### Riesgos
- Si en el futuro aparece un partner empresarial grande dispuesto a integrar pero no a aceptar AGPL → necesitamos articular el dual-licensing antes de que se vaya. Mitigación: tener la opción preparada.

## Referencias

- [GNU AGPLv3 full text](https://www.gnu.org/licenses/agpl-3.0.txt)
- [Choose a License: AGPL vs others](https://choosealicense.com/licenses/agpl-3.0/)
- [SSPL is not OSI-approved](https://opensource.org/blog/the-sspl-is-not-an-open-source-license)
- [`LICENSE`](../../LICENSE)
