# GitHub Actions – Shared CI/CD Workflows

This repository contains **reusable GitHub Actions workflows** used across CMZ projects.

---

## 🎯 Purpose

- Centralize CI/CD workflows shared by multiple repositories
- Avoid duplication of deployment logic
- Enforce consistent security and deployment practices
- Enable scalable growth as more services are added

This repository **does not** contain application code.

---

## 🏗️ Current Workflows

### `deploy-tailscale-compose.yml`

Reusable deployment workflow based on:

- **GitHub Actions**
- **Tailscale (zero-trust networking)**
- **Docker Compose**
- **Private infrastructure (no public IPs)**

#### Key characteristics

- No public SSH
- No exposed ports
- No VPN server
- Identity-based access (Tailscale + GitHub)
- Ephemeral CI runners
- Full audit trail via Tailscale Admin


---

## 🚀 How It Is Used

Application repositories **call** the reusable workflow from their own deployment workflows.

### Example (application repository)

```yaml
jobs:
  deploy_test:
    uses: cmz-mtm/github-actions/.github/workflows/deploy-tailscale-compose.yml@v1
    with:
      environment: test
      server: ${{ vars.DEPLOY_SERVER_TEST }}
    secrets:
      TAILSCALE_AUTHKEY: ${{ secrets.TAILSCALE_AUTHKEY }}
```

---

## ⚙️ Required Inputs

| Input        | Description                                   | Example     |
|-------------|-----------------------------------------------|-------------|
| environment | Deployment environment label                  | `test`      |
| server      | Tailscale MagicDNS name of the target server  | `testsrv01` |

### Optional Inputs

| Input        | Default     | Description                          |
|-------------|-------------|--------------------------------------|
| deploy_user | `deploy`    | SSH user on the target server        |
| apps_root   | `/srv/apps` | Base directory where apps are stored |

---

## 🔑 Required Secrets

| Secret            | Description                                  |
|-------------------|----------------------------------------------|
| TAILSCALE_AUTHKEY | Tailscale auth key for CI runner authentication |

Secrets must be defined at **organization** or **repository** level in the calling repository.

---

## 🏷️ Versioning

Reusable workflows are versioned using Git tags.

Application repositories should always reference a **fixed version**, for example:

```yaml
@v1
```

Do **not** reference `main` directly.

```
git tag v1
git push origin v1
```

---

## 📌 Notes

- This repository is intentionally kept small and focused
- Changes here can affect multiple services
- Prefer additive changes over breaking ones
- Breaking changes should result in a new major tag (`v2`, `v3`, …)

---

Maintained by the CMZ engineering team.
