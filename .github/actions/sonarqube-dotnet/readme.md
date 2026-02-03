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
    runs-on: ubuntu-latest
    steps:
      - name: SonarQube analysis (.NET)
        uses: CMZ-MTM/infra-github-actions/.github/actions/sonarqube-dotnet@v1
        with:
          # Project identity (SonarQube)
          sonar_project_key: translations-backend
          sonar_project_name: "Translations Backend"   # optional

          # Build/test target
          solution_path: src/TranslationsBackend.API.sln
          dotnet_version: "10.x"                       # optional

          # Coverage (optional)
          opencover_reports_paths: "**/coverage.opencover.xml"

          # Secrets are passed as inputs (caller decides secret names)
          tailscale_authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
          sonarqube_host_url: ${{ secrets.SONARQUBE_HOST_URL }}
          sonarqube_token: ${{ secrets.SONARQUBE_TOKEN }}
          package_user: ${{ secrets.PACKAGE_USER }}
          package_read_token: ${{ secrets.PACKAGE_READ }}
```

---

## 🔧 Inputs

| Name                      | Required | Description                                                                                 |
| ------------------------- | :------: | ------------------------------------------------------------------------------------------- |
| `sonar_project_key`       |    ✅     | SonarQube project key (technical identifier)                                                |
| `sonar_project_name`      |    ❌     | SonarQube project name (UI only)                                                            |
| `solution_path`           |    ✅     | Path to the `.sln` file                                                                     |
| `dotnet_version`          |    ❌     | .NET SDK version (default: `10.x`)                                                          |
| `opencover_reports_paths` |    ❌     | Glob for OpenCover reports (default: `**/coverage.opencover.xml`)                           |
| `tailscale_authkey`       |    ✅     | Tailscale auth key (tagged for CI, e.g. `tag:ci`)                                           |
| `sonarqube_host_url`      |    ✅     | SonarQube URL (include protocol + port, e.g. `http://sonar:8002`)                           |
| `sonarqube_token`         |    ✅     | SonarQube token                                                                             |
| `package_user`            |    ✅     | GitHub Packages username                                                                    |
| `package_read_token`      |    ✅     | GitHub Packages token (`read:packages` + `repo` if private repos/packages)                  |
| `nuget_source_name`       |    ❌     | NuGet source name to add/remove (default: `CMZ-MTM`)                                        |
| `nuget_source_url`        |    ❌     | GitHub Packages NuGet feed URL (default: `https://nuget.pkg.github.com/cmz-mtm/index.json`) |
| `nuget_config_file`       |    ❌     | NuGet.Config file to modify (repo-relative, default: `NuGet.Config`)                        |

---

## 🔐 Secrets

This action is designed to work even when the infra repository is **public**.

Secrets are **not** accessed directly inside the action. Instead, you pass secrets
from the consuming repository using the `with:` inputs:

```yaml
with:
  tailscale_authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
  sonarqube_host_url: ${{ secrets.SONARQUBE_HOST_URL }}
  sonarqube_token: ${{ secrets.SONARQUBE_TOKEN }}
  package_user: ${{ secrets.PACKAGE_USER }}
  package_read_token: ${{ secrets.PACKAGE_READ }}
```

This lets each repository:
- use its own secret naming convention
- keep secrets scoped to the consuming repo/org
- keep the infra repo public

---

## 📊 Test Coverage

> 🚧🚧 WORK IN PROGRESS 🚧🚧

Coverage is generated using Coverlet in OpenCover format and can be imported by SonarQube
if the report paths match your repository layout.

---
