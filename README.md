# Temporal Workflow Orchestration

Temporal workflow orchestration platform deployment.

## Helm Chart

Chart location: `deploy/helm/temporal/`

## Auth Boundary

- This repo owns chart capability and OIDC bootstrap wiring only.
- Environment-specific auth domains, client IDs, secrets, and authorization semantics live in `k8s/bbi-infrastructure`.
- Read [`docs/auth/AUTH_INTEGRATION_CONTRACT.md`](docs/auth/AUTH_INTEGRATION_CONTRACT.md) before changing Temporal auth behavior.

## Features

- OAuth2-proxy ready
- External Secrets support
- HPA enabled
- Production-ready configuration
