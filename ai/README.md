# ai/

Asistente IA basado en Claude API con tool use sobre los datos del usuario.

## Estado

Vacío. Se llenará a partir de **M5** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
ai/
├── pyproject.toml
├── prompts/
│   ├── system.md                   # system prompt versionado
│   └── examples/                   # few-shot ejemplos
├── tools/
│   ├── get_sensor_history.py
│   ├── get_recent_alerts.py
│   ├── get_device_config.py
│   └── get_calibration_status.py
├── service.py                      # chat loop con prompt caching
├── eval/                           # set de evaluación, métricas
└── tests/
```

## Modelo

- **Default**: `claude-sonnet-4-6` (rápido + barato + calidad alta para razonamiento sobre series temporales).
- **Fallback rápido**: `claude-haiku-4-5` para resúmenes triviales.
- **Prompt caching** habilitado en system + tool definitions (TTL 5min, ahorra ~85% del coste en chat conversacional).

## Tool use

Las tools están envueltas con verificación de permisos: cada llamada respeta el `org_id` del usuario autenticado. La IA **no puede** leer datos de otra organización aunque la pida explícitamente.

## Eval

Set de prompts representativos en `eval/`. Métricas: accuracy en preguntas de hechos, helpfulness en preguntas abiertas (LLM-as-judge), cost per conversation. Re-corrida cada vez que se cambia system prompt o se sube de versión de modelo.
