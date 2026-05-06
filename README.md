# Zailaboratory Organization Shared Configs

This repository contains organization-wide GitHub Actions reusable workflows.

## Reusable Code Scan Workflow

Automated weekly code security scanning that adapts to any tech stack.

### Quick Start

Add this file to your repository at `.github/workflows/code-scan.yml`:

```yaml
name: Code Scan
on:
  schedule:
    - cron: '0 1 * * 1'  # Weekly Monday 9AM UTC+8
  workflow_dispatch:

jobs:
  scan:
    uses: Zailaboratory/.github/.github/workflows/reusable-code-scan.yml@main
    secrets: inherit
```

That's it. The workflow will auto-detect your tech stack and run appropriate scans.

### Auto-Detection

| Detected File | Language | Scans Triggered |
|---|---|---|
| `pyproject.toml` / `requirements.txt` | Python | Ruff, Bandit, Semgrep |
| `package.json` | Node.js/TypeScript | tsc, npm audit, Semgrep |
| `go.mod` | Go | go vet, staticcheck, Semgrep |
| `Dockerfile` / `docker-compose.yml` | Docker | Trivy config scan |

Supports monorepo layouts: `backend/`, `frontend/`, `server/`, `web/`, `client/`, `api/`.

### Always-On Scans (all repos)

- **Trivy**: Dependency vulnerability scanning
- **Gitleaks**: Secret/credential leak detection
- **Semgrep**: Deep SAST (auto-selects rules by language)

### Optional Inputs

```yaml
jobs:
  scan:
    uses: Zailaboratory/.github/.github/workflows/reusable-code-scan.yml@main
    secrets: inherit
    with:
      notify_email: "team@example.com"   # Override recipients
      skip_python: true                   # Skip Python scans
      skip_node: true                     # Skip Node.js scans
      trivy_severity: "CRITICAL,HIGH"     # Only critical/high
      extra_semgrep_rules: "p/owasp-top-ten"  # Extra rules
```

### Required Secrets

**Organization level** (Settings > Secrets > Actions — shared by all repos):

| Secret | Description |
|---|---|
| `AZURE_TENANT_ID` | Azure AD tenant ID |
| `AZURE_CLIENT_ID` | App registration client ID |
| `AZURE_CLIENT_SECRET` | App registration secret |
| `MAIL_SENDER` | Sender email (must be a real mailbox) |

**Repository level** (each repo configures its own):

| Secret | Description |
|---|---|
| `NOTIFY_EMAIL` | Recipients for this repo's scan report (semicolon-separated) |

> Each repo sets its own `NOTIFY_EMAIL` so scan results go to the right team.
> If not set at repo level, it falls back to the org-level secret (if configured).
