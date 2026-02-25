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

A centralized GitHub Actions workflow that securely deploys applications via Tailscale using Docker Compose, enforces production access control, optionally runs migrations based on repo variables, and keeps all project deploy workflows standardized and minimal.

<a href="/.github/workflows/deploy-tailscale-compose.README.md ">View Documentation</a>

---

### `docsearch-reindex.yml`

A workflow that reindexes Typesense DocSearch data by running the remote scraper over Tailscale, either manually via `workflow_dispatch` (with `site_name`) or automatically after a successful `Deploy` workflow run.

---

## 🏗️ Current Actions

### `build-backend-container`

A centralized GitHub action that generates optimized Docker images for backend services, ensuring consistent build processes and configurations across multiple repositories.

<a href="/.github/actions/build-backend-container/README.md ">View Documentation</a>

---
### `build-migrations-container`

A centralized GitHub action that generates optimized Docker images for database migration tasks, ensuring consistent build processes and configurations across multiple repositories.

<a href="/.github/actions/build-migrations-container/README.md ">View Documentation</a>

---
### `sonarqube-dotnet`

A centralized GitHub action that runs SonarQube analysis for .NET projects using a self-hosted SonarQube instance over Tailscale, standardizing code quality checks across multiple repositories.

<a href="/.github/actions/sonarqube-dotnet/README.md ">View Documentation</a>

---
### `sonarqube-react`

A centralized GitHub action that runs SonarQube analysis for frontend (React/TypeScript) projects via a self-hosted SonarQube instance over Tailscale, providing consistent static code analysis across repositories.

<a href="/.github/actions/sonarqube-react/README.md ">View Documentation</a>

---

### `typesense-docsearch-reindex`

A centralized GitHub action that connects over Tailscale and runs the remote Typesense DocSearch reindex scripts for a selected site config.

<a href="/.github/actions/typesense-docsearch-reindex/README.md ">View Documentation</a>

---



## 🔁 Versioning Strategy

This repository **uses versioned workflows**.

### Version tags
- `v1` → Stable, backwards-compatible
- `v2` → Breaking changes

### Rules
- Bug fixes and non-breaking improvements update the same major version
- Breaking changes require a new major version
- Consuming repositories must explicitly opt-in to upgrades

### Managing versions

Versions are implemented using **Git tags**.

#### Create a new major version (example: v1)

```bash
git checkout master
git pull
git tag v1
git push origin v1
```

This creates a stable entry point that consuming repositories can reference:

```yaml
uses: CMZ-MTM/infra-github-actions/.github/workflows/sonar-dotnet.yml@v1
```

#### Update an existing major version (non-breaking changes)

If you add bug fixes or backwards-compatible improvements:

```bash
git checkout master
git pull
git tag -f v1
git push origin v1 --force
```

⚠️ This intentionally moves the `v1` tag forward. Only do this for non-breaking changes.

#### Create a new breaking version (example: v2)

When introducing breaking changes (inputs, behavior, secrets):

```bash
git checkout master
git pull
git tag v2
git push origin v2
```

Consuming repositories must explicitly opt in:

```yaml
uses: CMZ-MTM/infra-github-actions/.github/workflows/sonar-dotnet.yml@v2
```

#### Delete a version (if necessary)

If a version tag was created by mistake:

```bash
git tag -d v1
git push origin :refs/tags/v1
```

⚠️ Deleting tags can break consumers. Only do this if you are certain no repositories depend on it.

---
