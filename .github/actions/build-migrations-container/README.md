# 🚀 Build docker database migration container action

This GitHub Action builds a Docker image for database migrations using a specified .NET solution and project file. It then pushes the built image to GitHub Container Registry (GHCR) with appropriate tags.

In your workflow, you can use this action as follows:

```yaml

- name: Publish Migration Container Image
  uses: cmz-mtm/infra-github-actions/.github/actions/build-migrations-container@v1
  with:          
    solution_path: IdentityService.sln
    project_path: src/CMZ.IdentityService.Api/CMZ.IdentityService.Api.csproj
    token: ${{ secrets.GITHUB_TOKEN }}
    package_user: ${{ secrets.PACKAGE_USER }}
    package_password: ${{ secrets.PACKAGE_READ }}
    github_actor: ${{ github.actor }}
    container_name: identity-migrations
    ef_project: src/CMZ.IdentityService
    ef_startup_project: src/CMZ.IdentityService.Api
    ef_context: ApplicationDbContext # optional

```