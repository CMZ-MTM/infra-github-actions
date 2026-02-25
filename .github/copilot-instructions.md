# Copilot Instructions for `infra-github-actions`

## Repository Context
- This repository contains reusable GitHub Actions workflows and composite actions.
- It does **not** contain application business logic.
- Prioritize reliability, security, readability, and backward compatibility for shared CI/CD components.

## Core Principles
1. Keep changes minimal and focused on the requested outcome.
2. Prefer explicit and deterministic behavior over clever shortcuts.
3. Preserve compatibility for existing consumers whenever possible.
4. Fail fast with clear error messages when required inputs/secrets are missing.
5. Treat all workflow/action code as production infrastructure.

## Workflow Authoring Best Practices
- Use clear names for jobs, steps, inputs, and outputs.
- Always define `permissions` with least privilege.
- Pin third-party actions to a full commit SHA when feasible.
- Use `concurrency` to avoid overlapping deployments when relevant.
- Set `timeout-minutes` on long-running jobs.
- Use `if:` guards to prevent unsafe execution paths.
- Keep shell scripts strict (`set -euo pipefail` for bash).
- Prefer environment variables and inputs over hardcoded values.
- Avoid duplicated logic; extract reusable parts into composite actions.
- Keep workflow interfaces stable and documented.

## Security Requirements
- Never print secrets, tokens, or sensitive environment variables.
- Never hardcode credentials, hosts, or private endpoints.
- Use GitHub Secrets/Variables for configurable sensitive values.
- Validate untrusted input before using it in shell commands.
- Quote variables in shell scripts to reduce injection risks.
- Avoid unnecessary token scopes and privileged runners.

## Versioning and Compatibility
- Follow semantic versioning expectations for shared workflows/actions.
- For breaking changes, create a new major version path/tag strategy.
- Keep backward-compatible defaults where possible.
- Document any behavior change in the related README.

## Documentation Expectations
- Update the relevant README when changing inputs, outputs, behavior, or required secrets.
- Include concise examples for new inputs or usage changes.
- Keep naming and wording consistent across workflows and docs.

## Testing and Validation
- Validate YAML syntax and workflow structure before finalizing changes.
- Prefer adding or updating lightweight validation steps when practical.
- Ensure changed paths, action references, and required files exist.
- Do not introduce unrelated refactors in the same change.

## Style Guide
- Keep YAML formatting consistent with existing repository files.
- Keep step scripts short; extract complex logic when needed.
- Use descriptive comments only when intent is not obvious.
- Avoid one-letter variable names in scripts.

## What Copilot Should Avoid
- Do not redesign existing workflow architecture unless requested.
- Do not add new dependencies/tools without a clear need.
- Do not weaken security checks for convenience.
- Do not introduce breaking interface changes silently.

## Preferred Response Behavior
When suggesting or generating changes:
1. Explain assumptions briefly.
2. Show only targeted edits.
3. Call out compatibility or security impact.
4. Mention any required README updates.
5. Keep recommendations actionable and concise.
