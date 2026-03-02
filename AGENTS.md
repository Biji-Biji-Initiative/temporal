# AGENTS.md

## Repository Overview
Temporal workflow orchestration platform for Biji-Biji Initiative pipelines. Manages durable workflows and activities for business process automation.

## Core Commands
- Install: `go mod download`
- Build: `go build ./cmd/server`
- Test: `go test ./...`
- Lint: `golangci-lint run`
- Dev server: `go run ./cmd/server start`

## Project Structure
- `workflows/` — Workflow definitions (durable business logic)
- `activities/` — Activity implementations (side effects, integrations)
- `worker/` — Worker configuration and registration
- `cmd/` — Entry points (server, worker, cli)
- `schema/` — Workflow/activity schemas

## Conventions
- Version workflows when making breaking changes
- Keep activities idempotent where possible
- Use signals for external workflow communication
- Test with temporalite locally before deploying

## Temporal Namespace
- Development: `biji-dev`
- Production: `biji-prod`
- Configure via: `TEMPORAL_NAMESPACE` environment variable

## Validation Requirements
Before marking work as complete:
- Run: `go test ./...`
- Run: `golangci-lint run`
- If you changed workflows, verify backward compatibility
- If you added activities, include integration tests

## Deployment (GKE)
- Namespace: `temporal`
- Deploy via: ArgoCD GitOps (infra repo)
- Ask before: modifying production namespaces, workflow breaking changes

## Boundaries
- ✅ Always: Version workflows on breaking changes, test with temporalite locally
- ⚠️ Ask First: Production namespace changes, workflow breaking changes, new external integrations
- 🚫 Never: Deploy untested workflows to production, skip workflow versioning

## Escalation Rules
Ask a human when:
- Workflow changes are backward incompatible
- New external service integrations needed
- Production workflow failures require investigation

---
Last updated: 2026-03-02
