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