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
    self_contained: 'false' # optional, defaults to 'true'

```

## ⚡ Build notes

- NuGet packages (`~/.nuget/packages`) are cached between runs, keyed on the `*.csproj` / `Directory.*.props` files.
- The startup project is built once in `Release` and the bundle is created with `--no-build`.
- `self_contained: 'false'` produces a framework-dependent bundle (~3x smaller; it does not ship its own .NET runtime). It requires `migrations/Dockerfile` to use an ASP.NET Core base image, which also shares its layers with the backend image:

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS migrator
WORKDIR /app
COPY --chmod=755 efbundle .
COPY appsettings.json .
ENTRYPOINT ["./efbundle"]
```

Use `COPY --chmod` instead of a separate `RUN chmod +x`: the `RUN` step copies the whole bundle into a second layer.