# 🚀 Deploy using Docker Compose + Tailscale

Reusable GitHub Actions workflow for shipping Docker Compose apps over Tailscale.

- No public SSH or exposed ports
- No VPN server to manage
- Identity-based access (Tailscale + GitHub)
- Ephemeral CI runners with full audit trail via Tailscale Admin

---

## 🛠️ What the workflow does

- Resolves deployment config from GitHub variables (server, user, app root, migrations)
- For prod, only allows actors listed in `DEPLOY_PROD_ALLOWED_ACTORS`
- Installs + connects Tailscale on the runner using `TAILSCALE_AUTHKEY`
- Optionally runs migrations via `docker compose` in `infra-${server}/${DEPLOY_MIGRATIONS_CONTAINER_NAME}`
- Deploys the app via `docker compose pull && docker compose up -d` in `infra-${server}/${repo}`

Target hosts must already have Docker + Docker Compose and the repo checked out at `${APPS_ROOT}/infra-${SERVER}/${REPO}`.

---

## 🎛️ Inputs

The following inputs need to be declared when calling the workflow.

| Input        | Description                                   | Values     |
|-------------|-----------------------------------------------|-------------|
| environment | Deployment environment label                  | `prod` or `test`      |

---

## 🔑 Required secret

The following secrets need to be declared when calling the workflow.

| Scope | Secret            | Description                                     |
|-------|-------------------|-------------------------------------------------|
| Org or repo | `TAILSCALE_AUTHKEY` | Tailscale auth key for the CI runner |

---

## 🌳 Variables

Though not passed directly to the workflow as inputs or secrets, the following variables need to be on the repository for the workflow to work.
In addition, there are two optional variables:

#### Custom install path
Allows using a differently named folder for the same application. This is useful when managing multiple deployments of the same service on a single server, avoiding conflicts between instances.

#### Migration override flag (migrations_needed)
Allows disabling migrations for a specific deployment, even if the repository-level variable (DEPLOY_MIGRATIONS_NEEDED) is set to 'yes'.
This is useful when you need fine-grained control, ensuring that migrations are skipped only for a particular workflow execution without affecting other deployments.

### ✅ Required

Set these in the calling repository (or org-level where noted).

| Scope | Variable | Purpose | Example |
|-------|----------|---------|---------|
| Repo  | `DEPLOY_SERVER_TEST` | MagicDNS hostname for test deployments | `testsrv01` |
| Repo  | `DEPLOY_SERVER_PROD` | MagicDNS hostname for prod deployments | `prodsrv01` |
| Org   | `DEPLOY_PROD_ALLOWED_ACTORS` | Comma/space-separated GitHub usernames permitted to deploy prod | `name-surname,another-user` |

---

### 🐘 Migrations (optional)

| Scope | Variable | Values | Notes |
|-------|----------|--------|-------|
| Repo  | `DEPLOY_MIGRATIONS_NEEDED` | `yes`/`no` | Enables the migrations step |
| Repo  | `DEPLOY_MIGRATIONS_CONTAINER_NAME` | string | Required when migrations are enabled (e.g. `myapp-migrations`) |

### 🧰 Other (optional)

| Scope | Variable | Default | Purpose |
|-------|----------|---------|---------|
| Repo  | `DEPLOY_USER` | `deploy` | SSH user on the target server |
| Repo  | `DEPLOY_APPS_ROOT` | `/srv/apps` | Base directory where apps are stored |

---

## 📦 How to call

The workflow is invoked from application repositories. Pin to a tag (see versioning).

```yaml
jobs:
  deploy_test:
    uses: cmz-mtm/github-actions/.github/workflows/deploy-tailscale-compose.yml@v1
    with:
      environment: test
    secrets:
      TAILSCALE_AUTHKEY: ${{ secrets.TAILSCALE_AUTHKEY }}
```

For prod, set `environment: prod` and ensure `DEPLOY_PROD_ALLOWED_ACTORS` and `DEPLOY_SERVER_PROD` are set.

---

## 🏷️ Versioning

### 🆕 Creating a new version

Reusable workflows are shipped via Git tags; always pin to a tag instead of `main`.

```bash
git tag v2
git push origin v2
```

Example usage pin: `cmz-mtm/github-actions/.github/workflows/deploy-tailscale-compose.yml@v2`

### ✏️ Editing an exiting version

> ⚠️ CAUTION
> Never do this when using breaking changes


```bash
# Delete the exiting tag
git tag -d v1
git push origin :refs/tags/v1

# Recreate the same tag
git tag v1
git push origin v1
```

---

## 📌 Notes

- Repo is intentionally small and focused
- Changes here can affect multiple services
- Prefer additive changes over breaking ones
- Breaking changes should result in a new major tag (`v2`, `v3`, …)

---

Maintained by the CMZ engineering team.
