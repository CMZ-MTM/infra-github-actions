# 🚀 Build docker backend container action

This GitHub Action builds a Docker image for backend services using a multi-stage build process. It optimizes the image size by separating the build and runtime environments, ensuring that only necessary files are included in the final image.

In your workflow, you can use this action as follows:

```yaml

- name: Publish Backend Container Image
  uses: cmz-mtm/infra-github-actions/.github/actions/build-backend-container@v1
  with:          
    solution_path: IdentityService.sln
    project_path: src/CMZ.IdentityService.Api/CMZ.IdentityService.Api.csproj
    token: ${{ secrets.GITHUB_TOKEN }}
    package_user: ${{ secrets.PACKAGE_USER }}
    package_password: ${{ secrets.PACKAGE_READ }}
    github_actor: ${{ github.actor }}
    container_name: identity-backend

```