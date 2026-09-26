# OpenJEV Support

This fork of [swisnl/DecisionEngine](https://github.com/swisnl/DecisionEngine) adds optional support for [OpenJEV](https://openjev.sh), a free community gateway to the same Jev model built by [TypeSafe](https://typesafe.ai).

TypeSafe remains the default engine. Anyone with a `TYPESAFE_API_KEY` sees zero behaviour change.

## What was added

| File | Change |
|---|---|
| `config/decision-engine.php` | New `openjev` named engine (same `jev` driver, OpenJEV endpoint/model/key); `JEV_PROVIDER=openjev` switches the default engine. |
| `.env.example` | Documented `OPENJEV_API_KEY` and `JEV_PROVIDER` env vars. |
| `README.md` | OpenJEV note after the intro; `openjev` row in the engine table; env var documentation. |
| `OPENJEV.md` | This file. |

No existing TypeSafe code was renamed, removed, or re-defaulted. The `JevEngine` class already accepted custom `base_url` and `model` parameters, so no engine code changes were needed.

## Provider selection rule

1. **Explicit choice wins:** `JEV_PROVIDER=openjev` makes `openjev` the default engine; `->using('openjev')` selects it per-decision.
2. **Otherwise, TypeSafe if its key is set:** `TYPESAFE_API_KEY` -> `jev` engine (unchanged default).
3. **Otherwise, OpenJEV if only `OPENJEV_API_KEY` is set:** set `JEV_PROVIDER=openjev` (or `DECISION_ENGINE=openjev`) to use it.

## Configuration

```bash
# Option A: Use OpenJEV as the default
export OPENJEV_API_KEY=your-openjev-key
export JEV_PROVIDER=openjev

# Option B: Keep TypeSafe as default, use OpenJEV per-decision
export TYPESAFE_API_KEY=your-typesafe-key
export OPENJEV_API_KEY=your-openjev-key
# Then in code: Decision::for($state)->using('openjev')->...
```

| Env var | Default | Description |
|---|---|---|
| `OPENJEV_API_KEY` | — | API key from the OpenJEV dashboard |
| `OPENJEV_BASE_URL` | `https://api.openjev.sh` | OpenJEV API base URL |
| `OPENJEV_DEFAULT_MODEL` | `openjev` | Model id for OpenJEV |
| `JEV_PROVIDER` | — | Set to `openjev` to use OpenJEV as the default engine |

## Endpoint and model mapping

| | TypeSafe (default) | OpenJEV |
|---|---|---|
| Endpoint | `https://api.typesafe.ai/v1/systemone` | `https://api.openjev.sh/v1/systemone` |
| Model | `jev-latest` | `openjev` |
| Key env | `TYPESAFE_API_KEY` | `OPENJEV_API_KEY` |

Both use the same request/response contract. The `RetryPolicy` already retries on 429, 503, and 5xx (including 529).

## Verification

A live POST to the OpenJEV systemone endpoint with model `openjev`, state `ping`, and one noul question returned HTTP 200 with a valid answer. No repository code was executed during this port.

## Upstream

Original project: https://github.com/swisnl/DecisionEngine by @swisnl (MIT license).
