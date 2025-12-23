# Deploy using docker compose and Tailscale


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

## Configurations in repo

### 🔑 Required Secrets

**Org level secrets**

| Secret            | Description                                     |
|-------------------|-------------------------------------------------|
| TAILSCALE_AUTHKEY | Tailscale auth key for CI runner authentication |

Secrets must be defined at **organization** or **repository** level in the calling repository.


### 🌳 Required Variables

**Org level variables**

| Variable                           | Values                                  | Example                     |
|------------------------------------|-----------------------------------------|-----------------------------|
| `DEPLOY_PROD_ALLOWED_ACTORS`       | Comma/space-separated GitHub usernames  | `name-surname,another-user` |


**Repo level variables**

| Variable                           | Values                                  | Example                     |
|------------------------------------|-----------------------------------------|-----------------------------|
| `DEPLOY_MIGRATIONS_NEEDED`         | `yes`, `no`                             | `yes`, `no`                 |
| `DEPLOY_MIGRATIONS_CONTAINER_NAME` | Name of the container                   | `myapp-migrations`          |

---

## 🚀 How to Call

Application repositories **call** the reusable workflow from their own deployment workflows.
The target webserver must already have Docker + Docker Compose and your repo checked out at `${apps_root}/infra-${server}/${repo}`.

**Example**

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

## ⚙️ Inputs 

The following inputs are used internally in the workflow

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
