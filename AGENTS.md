# Agent Guide (temporal deployment)

This repo contains the **Helm chart** for deploying Temporal used by `BBI-K8`.

## Key Paths

- Helm chart: `deploy/helm/temporal/`

## Deployment Contract (Important)

- Keep the chart **environment-agnostic**.
- Environment-specific values live in `BBI-K8/apps/temporal/overlays/<env>/values.yaml`.
- Do not commit secrets (OIDC client secrets, DB passwords).

## Common Commands

```bash
helm lint deploy/helm/temporal
```

