# CLAUDE.md

Temporal workflow orchestration platform deployment.

## Overview

Production Temporal deployment for durable workflow execution, configured for Kubernetes with OAuth2-proxy authentication and external secrets.

## Tech Stack

| Component | Purpose |
|-----------|---------|
| Temporal | Workflow orchestration |
| PostgreSQL | Backend database |
| Helm | Deployment |

## Directory Structure

```
temporal/
├── deploy/
│   └── helm/
│       └── temporal/     # Helm chart
├── AGENTS.md
└── README.md
```

## Key Commands

```bash
# Lint Helm chart
helm lint deploy/helm/temporal

# Template with values
helm template temporal deploy/helm/temporal -f <values-file>

# Check pods
kubectl get pods -n temporal

# View logs
kubectl logs -n temporal -l app=temporal -f
```

## Features

- OAuth2-proxy ready (Authentik integration)
- External Secrets support (GCP Secret Manager)
- HPA enabled for autoscaling
- Production-ready configuration
