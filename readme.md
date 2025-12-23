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

Reusable deployment workflow for shipping Docker Compose apps over Tailscale. It relies on:

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

Application repositories **call** the reusable workflow from their own deployment workflows. The target host must already have Docker + Docker Compose and your repo checked out at `${apps_root}/infra-${server}/${repo}`.

### Minimal example (test)

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

### With migrations enabled (prod)

```yaml
jobs:
  deploy_prod:
    uses: cmz-mtm/github-actions/.github/workflows/deploy-tailscale-compose.yml@v1
    with:
      environment: prod
      server: ${{ vars.DEPLOY_SERVER_PROD }}
      deploy_user: deploy
      apps_root: /srv/apps
    secrets:
      TAILSCALE_AUTHKEY: ${{ secrets.TAILSCALE_AUTHKEY }}
    vars:
      DEPLOY_PROD_ALLOWED_ACTORS: "octocat,another-user"
      DEPLOY_MIGRATIONS_NEEDED: "true"
      DEPLOY_MIGRATIONS_CONTAINER_NAME: "myapp-migrations"
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

## 🌳 Required Variables (in calling repo)

- `DEPLOY_PROD_ALLOWED_ACTORS`: required for prod; comma/space-separated GitHub usernames allowed to deploy prod.
- `DEPLOY_MIGRATIONS_NEEDED`: optional; set to yes/true/1 to run the migrations compose project before the main app.
- `DEPLOY_MIGRATIONS_CONTAINER_NAME`: required only when migrations are enabled; directory/container name under `${apps_root}/infra-${server}`.

---

## 🏷️ Versioning

Reusable workflows are shipped via Git tags; always pin to a tag instead of `main`.

Create or bump a tag:

```bash
git tag v1
git push origin v1
```

Example usage pin: `cmz-mtm/github-actions/.github/workflows/deploy-tailscale-compose.yml@v1`

---

## 📌 Notes

- This repository is intentionally kept small and focused
- Changes here can affect multiple services
- Prefer additive changes over breaking ones
- Breaking changes should result in a new major tag (`v2`, `v3`, …)

---

Maintained by the CMZ engineering team.
