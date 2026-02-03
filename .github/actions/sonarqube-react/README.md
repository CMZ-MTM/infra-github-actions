# 🚀 Quality Analysis using SonarQube (local CMZ instance)

Reusable GitHub **action** that runs **SonarQube analysis** for **frontend projects**
(React with TypeScript / JavaScript) against a **self-hosted SonarQube instance**
reachable via **Tailscale**.

**Features**
- GitHub-hosted runners
- Tailscale private networking
- Node.js runtime setup
- SonarScanner CLI
- Source code analysis (no build, no tests)
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
      - name: SonarQube analysis (Frontend)
        uses: CMZ-MTM/infra-github-actions/.github/actions/sonarqube-react@v1
        with:
          # Project identity (SonarQube)
          sonar_project_key: translations-frontend
          sonar_project_name: "Translations Frontend"   # optional

          # Repo structure
          project_base_dir: "."                          # optional
          node_version: "20.x"                           # optional

          # Secrets are passed as inputs
          tailscale_authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
          sonarqube_host_url: ${{ secrets.SONARQUBE_HOST_URL }}
          sonarqube_token: ${{ secrets.SONARQUBE_TOKEN }}
```

---

## 🔧 Inputs

| Name                 | Required | Description                                                       |
| -------------------- | :------: | ----------------------------------------------------------------- |
| `sonar_project_key`  |    ✅     | SonarQube project key (technical identifier)                      |
| `sonar_project_name` |    ❌     | SonarQube project name (UI only)                                  |
| `project_base_dir`   |    ❌     | Directory to scan from (repo-relative, default: `.`)              |
| `node_version`       |    ❌     | Node.js version (default: `20.x`)                                 |
| `tailscale_authkey`  |    ✅     | Tailscale auth key (tagged for CI, e.g. `tag:ci`)                 |
| `sonarqube_host_url` |    ✅     | SonarQube URL (include protocol + port, e.g. `http://sonar:8002`) |
| `sonarqube_token`    |    ✅     | SonarQube token                                                   |

---

## 🔐 Secrets

This action is designed to work even when the infra repository is **public**.

Secrets are **not** accessed directly inside the action. Instead, they are passed
from the consuming repository as inputs:

```yaml
with:
  tailscale_authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
  sonarqube_host_url: ${{ secrets.SONARQUBE_HOST_URL }}
  sonarqube_token: ${{ secrets.SONARQUBE_TOKEN }}
```

---

## 🧠 How it works

This action:
1. Checks out the repository with full Git history (for blame & SCM data)
2. Connects to the private network using Tailscale
3. Sets up the requested Node.js version
4. Installs the SonarScanner CLI
5. Runs a source-only SonarQube scan

No build or test step is required.

---

## 📊 Test Coverage

> 🚧🚧 NOT IMPLEMENTED 🚧🚧

This action currently **does not collect or import coverage**.

---