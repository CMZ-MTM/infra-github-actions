# GitHub Actions – Shared CI/CD Workflows

This repository contains **reusable GitHub Actions workflows** used across CMZ projects.

Its main goal is to centralize **deployment logic** so that application repositories remain clean, consistent, and easy to maintain.

---

## 🎯 Purpose

- Centralize CI/CD workflows shared by multiple repositories
- Avoid duplication of deployment logic
- Enforce consistent security and deployment practices
- Enable scalable growth as more services are added

This repository **does not** contain application code.

---

## 🏗️ Current Workflows

### `deploy.yml`

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

## 🔐 Security Model

- CI runners join the Tailnet using **Tailscale OAuth**
- Runners are tagged as `tag:ci`
- Servers are tagged as `tag:servers`
- Access is controlled via Tailscale ACLs
- No persistent credentials are stored on servers

Production deployments are protected via:
- Branch protection rules
- Job-level gating based on `github.actor`
- Separate deployment targets

---

## 🚀 How It Is Used

Application repositories **call** the reusable workflow:

```yaml
uses: cmz/github-actions/.github/workflows/deploy.yml@v1
