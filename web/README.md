# web/

Aplicación web. Next.js 15 (App Router) + Tailwind v4 + shadcn/ui + TypeScript estricto.

## Estado

Vacío. Se llenará a partir de **M4** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
web/
├── package.json
├── tsconfig.json
├── tailwind.config.ts
├── next.config.ts
├── app/                    # App Router
│   ├── (auth)/             # login, register, recuperar
│   ├── (app)/              # dashboard, devices, alerts, calibration, settings
│   └── api/                # route handlers (proxies)
├── components/             # shadcn/ui + custom
├── lib/                    # fetchers, utils
├── hooks/                  # React Query hooks
├── services/               # cliente API tipado desde OpenAPI
├── types/                  # tipos compartidos
└── e2e/                    # Playwright
```

## Run local

```bash
cd web
pnpm install
pnpm dev                    # http://localhost:3000
pnpm test                   # vitest
pnpm e2e                    # playwright
pnpm lint
pnpm build && pnpm start
```

## Decisiones

- **Auth**: JWT del backend persistido en cookie HttpOnly. Refresh automático vía interceptor.
- **Fetching**: TanStack Query con cliente tipado generado desde OpenAPI.
- **Live updates**: WebSocket directo al backend en `/devices/{id}/live`.
- **Charts**: `tremor` o `recharts` (decidir en M4).
- **Dark mode**: `next-themes`, default = system.
