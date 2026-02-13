# Agent Guide (temporal deployment)

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

---

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


## Landing the Plane (Session Completion)

**When ending a work session**, you MUST complete ALL steps below. Work is NOT complete until `git push` succeeds.

**MANDATORY WORKFLOW:**

1. **File issues for remaining work** - Create issues for anything that needs follow-up
2. **Run quality gates** (if code changed) - Tests, linters, builds
3. **Update issue status** - Close finished work, update in-progress items
4. **PUSH TO REMOTE** - This is MANDATORY:
   ```bash
   git pull --rebase
   bd sync
   git push
   git status  # MUST show "up to date with origin"
   ```
5. **Clean up** - Clear stashes, prune remote branches
6. **Verify** - All changes committed AND pushed
7. **Hand off** - Provide context for next session

**CRITICAL RULES:**
- Work is NOT complete until `git push` succeeds
- NEVER stop before pushing - that leaves work stranded locally
- NEVER say "ready to push when you are" - YOU must push
- If push fails, resolve and retry until it succeeds

## Specs & Docs Convention

This project uses the **specs-vs-docs** convention from [team-skills](https://github.com/Biji-Biji-Initiative/team-skills).

### Quick Reference
- **Specs** (testable contracts): `specs/` — define WHAT MUST BE TRUE
- **Docs** (explanations): `docs/` — explain what IS and HOW TO USE IT
- **Config**: `specdocs.config.yml`

### Rules
- Before implementing any feature, check if a spec exists in `specs/`
- Specs use normative language: MUST, SHOULD, MAY (RFC 2119)
- Acceptance Criteria use stable IDs: `AC-001`, `AC-002`, etc.
- Each AC maps to a verification method (automated/monitoring/manual)

### Key Tools
| Tool | Purpose |
|------|---------|
| `spec_lint.py specs/` | Lint specs for structure compliance |
| `spec_verify.py specs/` | Verify AC-IDs + testmap coverage |
| `spec_fix.py specs/ --add-ac-ids` | Bulk-add AC-IDs to checkboxes |

### Spec Template
New specs should follow `specs/_TEMPLATE.md`. Required sections: Scope, Non-goals, Requirements, Acceptance Criteria, Edge Cases, Observability, Rollout & Rollback, Open Questions.
