# AGENTS.md

## Repository Overview
Temporal workflow orchestration platform for Biji-Biji Initiative pipelines. Contains Helm chart for Kubernetes deployment. Manages durable workflows and activities for business process automation.

## Core Commands
- Install: `go mod download`
- Build: `go build ./cmd/server`
- Test: `go test ./...`
- Lint: `golangci-lint run`
- Dev server: `go run ./cmd/server start`
- Helm lint: `helm lint deploy/helm/temporal`

## Project Structure
- `deploy/helm/temporal/` — Helm chart for Kubernetes deployment
- `workflows/` — Workflow definitions (durable business logic)
- `activities/` — Activity implementations (side effects, integrations)
- `worker/` — Worker configuration and registration
- `cmd/` — Entry points (server, worker, cli)
- `schema/` — Workflow/activity schemas

## Deployment Contract (Important)
- Keep the chart **environment-agnostic**
- Environment-specific values live in `BBI-K8/apps/temporal/overlays/<env>/values.yaml`
- Do not commit secrets (OIDC client secrets, DB passwords)

## CRITICAL - Data Protection Rules

> **Master policy:** https://github.com/Biji-Biji-Initiative/BBI-K8/blob/main/docs/DATA_PROTECTION.md

### Forbidden Actions (Require Explicit User Confirmation)
1. Delete PVCs, PVs, or namespaces containing databases
2. Delete or scale StatefulSets/Deployments with databases to 0
3. Modify volumeClaimTemplates in StatefulSets
4. Run database DROP/TRUNCATE/DELETE commands
5. Delete Helm releases containing databases
6. Modify storage configurations that could cause data loss

### Before Any Risky Operation
Always create a Velero backup first:
```bash
velero backup create pre-op-<namespace>-$(date +%Y%m%d-%H%M) --include-namespaces <namespace> --wait
```
Then ask for explicit user confirmation before proceeding.

**Backup Docs:** https://github.com/Biji-Biji-Initiative/BBI-K8/blob/main/docs/BACKUP_AND_RECOVERY.md

## Temporal Namespace
- Development: `biji-dev`
- Production: `biji-prod`
- Configure via: `TEMPORAL_NAMESPACE` environment variable

## Conventions
- Version workflows when making breaking changes
- Keep activities idempotent where possible
- Use signals for external workflow communication
- Test with temporalite locally before deploying

## Validation Requirements
Before marking work as complete:
- Run: `go test ./...`
- Run: `golangci-lint run`
- Run: `helm lint deploy/helm/temporal`
- If you changed workflows, verify backward compatibility
- If you added activities, include integration tests

## Deployment (GKE)
- Namespace: `temporal`
- Deploy via: ArgoCD GitOps (BBI-K8 repo)
- Ask before: modifying production namespaces, workflow breaking changes

## Boundaries
- ✅ Always: Version workflows on breaking changes, test with temporalite locally, create Velero backups before risky ops
- ⚠️ Ask First: Production namespace changes, workflow breaking changes, new external integrations, any data-affecting operations
- 🚫 Never: Deploy untested workflows to production, skip workflow versioning, delete PVCs/databases without explicit confirmation

## Escalation Rules
Ask a human when:
- Workflow changes are backward incompatible
- New external service integrations needed
- Production workflow failures require investigation
- Any operation affecting databases or persistent storage

## Landing the Plane (Session Completion)

When ending a work session, complete ALL steps below. Work is NOT complete until `git push` succeeds.

**Mandatory Workflow:**
1. File issues for remaining work
2. Run quality gates (tests, linters, builds)
3. Update issue status
4. Push to remote:
   ```bash
   git pull --rebase
   git push
   git status  # MUST show "up to date with origin"
   ```

---
Last updated: 2026-03-02
