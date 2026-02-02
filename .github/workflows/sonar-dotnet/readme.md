# 🚀 Quality Analysis using SonarQube (local CMZ instance)

Reusable workflow that runs **SonarQube analysis** for .NET projects
against a **self-hosted SonarQube instance** reachable via **Tailscale**.

**Features**
- GitHub-hosted runners
- Tailscale private networking
- .NET SDK setup
- SonarScanner for .NET
- Build + unit tests
- Optional test coverage import
- Designed for SonarQube Community Edition

---

## 🚀 Usage

In the consuming repository, create a workflow like:

```yaml
name: SonarQube analysis

on:
  workflow_dispatch:
  schedule:
    - cron: "0 7 * * 1"   # Weekly

jobs:
  sonar:
    uses: CMZ-MTM/infra-github-actions/.github/workflows/sonar-dotnet.yml@v1
    with:
      project-key: translations-backend
      project-name: "Translations Backend"
      solution-path: src/TranslationsBackend.API.sln
    secrets:
      SONARQUBE_HOST_URL: ${{ secrets.SONARQUBE_HOST_URL }}
      SONARQUBE_TOKEN: ${{ secrets.SONARQUBE_TOKEN }}
      TAILSCALE_AUTHKEY: ${{ secrets.TAILSCALE_AUTHKEY }}
      PACKAGE_USER: ${{ secrets.PACKAGE_USER }}
      PACKAGE_READ: ${{ secrets.PACKAGE_READ }}
```

---

## 🔧 Inputs

| Name                   | Required | Description                                  |
| ---------------------- | -------- | -------------------------------------------- |
| `project-key`          | ✅        | SonarQube project key (technical identifier) |
| `project-name`         | ❌        | SonarQube project display name               |
| `solution-path`        | ✅        | Path to the `.sln` file                      |
| `dotnet-version`       | ❌        | .NET SDK version (default: `10.x`)           |
| `sonar-opencover-path` | ❌        | Glob path for OpenCover reports              |

---

## 🔐 Required Secrets

### SonarQube

| Scope | Secret               | Description                                      |
| ----- | -------------------- | ------------------------------------------------ |
| Org   | `SONARQUBE_HOST_URL` | Full SonarQube URL (including protocol and port) |
| Org   | `SONARQUBE_TOKEN`    | SonarQube analysis token                         |

### Tailscale

| Scope | Secret              | Description                |
| ----- | ------------------- | -------------------------- |
| Org   | `TAILSCALE_AUTHKEY` | Tagged auth key (`tag:ci`) |

> ⚠️ Auth keys are currently supported.  
> OAuth clients may be adopted in the future.

### GitHub Packages (NuGet)

| Scope | Secret         | Description                                      |
| ----- | -------------- | ------------------------------------------------ |
| Org   | `PACKAGE_USER` | GitHub username                                  |
| Org   | `PACKAGE_READ` | Token with `read:packages` (+ `repo` if private) |

---

## 📊 Test Coverage

> 🚧🚧 WORK IN PROGRESS 🚧🚧

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

Versions are implemented using **Git tags** (recommended) or **long-lived branches**.

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

