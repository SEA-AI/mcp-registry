# MCP Registry

Company-wide [MCP registry](https://docs.github.com/en/copilot/how-tos/administer-copilot/manage-mcp-usage/configure-mcp-registry) for GitHub Copilot. This repo hosts a static JSON catalog of approved MCP servers, served via GitHub Pages.

It does **not** run any code — it only points to MCP server manifests that IDEs and Copilot discover automatically.

## Registered Servers

| Server | Version | Transport | Package |
|--------|---------|-----------|---------|
| [FiftyOne MCP Server](https://github.com/voxel51/fiftyone-mcp-server) | 0.1.3 | stdio | `uvx fiftyone-mcp-server` |

## How It Works

The directory structure under `v0.1/` maps directly to the [v0.1 MCP registry spec](https://registry.modelcontextprotocol.io/docs) endpoints:

```
GET /v0.1/servers                                          → server listing
GET /v0.1/servers/{name}/versions/latest                   → latest version
GET /v0.1/servers/{name}/versions/{version}                → specific version
```

GitHub Pages auto-serves `index.html` for directory paths, so each endpoint is a directory containing an `index.html` file with the JSON response body.

## Setup

1. Push this repo to your GitHub organization (e.g. `sea-ai/mcp-registry`).
2. Go to **repo Settings → Pages → Source** and select **GitHub Actions**.
3. The included workflow (`.github/workflows/deploy.yml`) deploys on every push to `main`.
4. Set the registry URL in your org:
   **GitHub Org → Settings → Copilot → Policies → MCP Registry URL**
   ```
   https://sea-ai.github.io/mcp-registry
   ```

## Adding a New Server

1. Create the version directory:
   ```
   v0.1/servers/<namespace>/<server-name>/versions/<version>/index.html
   ```
2. Copy the JSON structure from an existing server entry and update the fields.
3. Create (or update) the `latest/index.html` symlink/copy for that server.
4. Add the new server to the listing in `v0.1/servers/index.html`.
5. Update the table in this README.
6. Push to `main` — GitHub Actions deploys automatically.

## Known Limitation

GitHub Pages serves `index.html` with `Content-Type: text/html`. VS Code and Copilot parse the JSON body regardless. If strict content-type headers are needed, place Cloudflare, Vercel, or Netlify in front to override response headers.

