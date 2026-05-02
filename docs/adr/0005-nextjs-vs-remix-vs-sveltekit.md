# ADR-0005: Next.js 15 (App Router) para la web

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

La app web tiene que:
- Servir un dashboard con gráficas en vivo (WebSocket).
- Tener auth flow completo (login, refresh, logout, recuperar password).
- Compartir tipos con la API (TypeScript estricto + cliente generado desde OpenAPI).
- SEO no es crítico (es app autenticada), pero la landing pública sí debería tener SSR.
- Despliegue self-hosted en Hetzner detrás de Caddy.

### Opciones evaluadas

| Framework | Pros | Contras |
|-----------|------|---------|
| **Next.js 15 (App Router)** | Ecosistema enorme, RSC para landing/SEO, soporte nativo de WebSocket en route handlers, integración shadcn/ui pulida | Vercel-centric en docs (autohostable, pero hay que querer hacerlo) |
| Remix / React Router 7 | API más simple, menos magia | Plugins/community más pequeño que Next |
| SvelteKit | DX más limpia, bundle más pequeño | Comunidad / librerías menor; hire-ability inferior |
| Astro + islas React | SSG rápido | No es la herramienta para app interactiva |
| Nuxt (Vue) | Maduro | Vue es minoritario en mi stack mental — fricción cero motivo |

## Decision

**Usamos Next.js 15 con App Router**, autohospedado en Hetzner.

- **TypeScript estricto** (`"strict": true`).
- **Tailwind v4** + **shadcn/ui** para componentes accesibles y consistentes.
- **TanStack Query** para fetching y cache.
- **NextAuth o auth custom** con JWT del backend persistido en cookie HttpOnly.
- **WebSocket** directo al backend (no proxy a través de route handlers, para simplificar reconexión).
- Charts: `tremor` o `recharts` (decisión definitiva en M4).

## Consequences

### Positivas
- Reusabilidad de componentes shadcn/ui — cero tiempo construyendo botones, modales, tablas.
- App Router con layouts anidados encaja bien con sidebar + header + nested routes.
- Server Components para la landing pública — SEO y first paint rápidos.
- `next/image`, `next/font`, optimizaciones gratis.

### Negativas
- App Router tiene su propia curva (Server vs Client components, Suspense, etc.). No es bloqueante.
- Build time mayor que SvelteKit/Remix.

### Neutras
- Despliegue: imagen Docker `node:22-alpine` + `next start`. Detrás de Caddy.

### Riesgos
- Next 15 sigue añadiendo features rápidas (Turbopack, etc.). Pinear versión major y subir en PRs dedicadas.

## Referencias

- [Next.js docs](https://nextjs.org/docs)
- [shadcn/ui](https://ui.shadcn.com/)
- [TanStack Query](https://tanstack.com/query/latest)
- ADR-0006 (React Native + Expo)
