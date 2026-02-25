# Typesense DocSearch Reindex

Reusable GitHub Action that connects to Tailnet and runs the remote Typesense scraper reindex script.

## Usage

```yaml
name: Typesense DocSearch Reindex

on:
  workflow_dispatch:


jobs:
  reindex:
    runs-on: ubuntu-latest
    permissions:
      contents: read
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Reindex DocSearch
        uses: CMZ-MTM/infra-github-actions/.github/actions/typesense-docsearch-reindex@v1
        with:
          tailscale_authkey: ${{ secrets.TAILSCALE_AUTHKEY }}
          typesense_server: ${{ vars.TYPESENSE_SERVER }}
          typesense_api_key: ${{ secrets.TYPESENSE_API_KEY }}
          mycmz_api_key: ${{ secrets.MYCMZ_ICON_MANUALS_API_KEY }}
          mycmz_api_secret: ${{ secrets.MYCMZ_ICON_MANUALS_API_SECRET }}
          site_name: ${{ github.event.repository.name }}
```

## Inputs

| Name | Required | Description |
| --- | :---: | --- |
| `tailscale_authkey` | ✅ | Tailscale auth key used to join the tailnet. |
| `typesense_server` | ✅ | Typesense server hostname reachable via Tailscale. |
| `typesense_api_key` | ✅ | Typesense API key exported on the remote server. |
| `mycmz_api_key` | ✅ | MYCMZ API key exported on the remote server. |
| `mycmz_api_secret` | ✅ | MYCMZ API secret exported on the remote server. |
| `site_name` | ❌ | Site config name in `scraper-native/configs` (without `.json`). Use repository name. |

## Required Variables and Secrets

- Variable: `TYPESENSE_SERVER`
- Secrets: `TAILSCALE_AUTHKEY`, `TYPESENSE_API_KEY`, `MYCMZ_ICON_MANUALS_API_KEY`, `MYCMZ_ICON_MANUALS_API_SECRET`
