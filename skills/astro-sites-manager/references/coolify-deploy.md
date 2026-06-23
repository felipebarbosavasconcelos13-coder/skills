# Deploying Astro on Coolify

Production-tested patterns for deploying Astro sites on self-hosted Coolify (v4.x). Based on 17+ live deployments.

---

## Build Pack Decision

| Scenario | build_pack | Notes |
|----------|-----------|-------|
| Astro v6+ (requires Node ≥22.12.0) | `dockerfile` | Nixpacks can't pin minor version |
| Astro v5 or earlier | `nixpacks` | `NIXPACKS_NODE_VERSION=22` works |
| Astro `output: 'static'` with package.json | `nixpacks` | start: `npx serve dist -l 80 -s` |
| Astro `output: 'static'` (Dockerfile) | `dockerfile` | nginx serves directly |
| HTML/CSS only (no package.json) | `static` | `static_image: nginx:alpine` |

**Rule:** For Astro v6+ and v7, always use `dockerfile`. Nixpacks resolves Node 22.11.0 from its internal nixpkgs archive, but Astro v6+ requires ≥22.12.0.

---

## Dockerfile — Astro SSR (Node Adapter)

```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
ENV NODE_OPTIONS="--max-old-space-size=512"

# Coolify injects env vars as ARG — must convert to ENV for npm run build
ARG MY_API_KEY
ARG PUBLIC_SITE_URL
ENV MY_API_KEY=$MY_API_KEY
ENV PUBLIC_SITE_URL=$PUBLIC_SITE_URL

COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:22-alpine
WORKDIR /app
COPY --from=build /app/dist ./dist
COPY --from=build /app/node_modules ./node_modules
COPY --from=build /app/package.json ./
ENV HOST=0.0.0.0
ENV PORT=4321
EXPOSE 4321
CMD ["node", "dist/server/entry.mjs"]
```

## Dockerfile — Astro Static (nginx)

```dockerfile
FROM node:22-alpine AS build
WORKDIR /app
ENV NODE_OPTIONS="--max-old-space-size=512"
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```

---

## Critical Gotchas

### ARG vs ENV — Build-time secrets

Coolify injects variables as Docker `ARG`. But `ARG` does NOT become an environment variable for child processes like `npm run build`. Astro/Vite resolves `import.meta.env.VAR` during build — if the variable doesn't exist in the process environment, it silently becomes `undefined`.

**Fix:** For every secret the build needs:
```dockerfile
ARG RESEND_API_KEY
ENV RESEND_API_KEY=$RESEND_API_KEY
```

### OOM on Resource-Limited Servers

The `astro build` process can die with exit code 255 and no clear error message on servers with limited RAM (~2GB).

**Fix:** Add to build stage:
```dockerfile
ENV NODE_OPTIONS="--max-old-space-size=512"
```

### Nixpacks Node Version

Nixpacks only accepts **major version**. `NIXPACKS_NODE_VERSION=22` can resolve to 22.11.0, causing:
```
Node.js v22.11.0 is not supported by Astro!
```

**Fix options:**
1. Use Dockerfile instead (recommended for Astro v6+)
2. Set `NIXPACKS_NODE_VERSION=24` (skips a major)
3. Pin nixpkgs archive via `nixpacks.toml`:
   ```toml
   [phases.setup]
   nixpkgsArchive = "5ef6c8a1bf89a0bfe4e15e7baf5bab7feeff86a5"
   ```

### Nixpacks Timeout

Nixpacks downloads ~600MB nixpkgs archive during build. On servers with limited bandwidth, build dies silently during `unpacking` step.

**Fix:** Switch to Dockerfile. `node:22-alpine` is ~50MB vs ~600MB.

---

## Coolify API — Create App

```bash
COOLIFY_URL="https://cool.example.com/api/v1"
COOLIFY_KEY="your-token"

curl -sS -X POST "$COOLIFY_URL/applications/private-deploy-key" \
  -H "Authorization: Bearer $COOLIFY_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "project_uuid": "PROJECT_UUID",
    "environment_name": "production",
    "server_uuid": "SERVER_UUID",
    "private_key_uuid": "SSH_KEY_UUID",
    "git_repository": "git@gitlab.com:user/project.git",
    "git_branch": "main",
    "build_pack": "dockerfile",
    "dockerfile_location": "/Dockerfile",
    "ports_exposes": "4321",
    "name": "my-astro-site"
  }'
```

### Set Domain

```bash
# Use "domains", NOT "fqdn" — fqdn returns "field not allowed"
curl -sS -X PATCH "$COOLIFY_URL/applications/$APP_UUID" \
  -H "Authorization: Bearer $COOLIFY_KEY" \
  -H "Content-Type: application/json" \
  -d '{"domains": "https://mysite.com"}'
```

### Set Environment Variables

```bash
curl -sS -X POST "$COOLIFY_URL/applications/$APP_UUID/envs" \
  -H "Authorization: Bearer $COOLIFY_KEY" \
  -H "Content-Type: application/json" \
  -d '{"key": "MY_VAR", "value": "secret-value", "is_preview": false}'
```

> Do NOT send `is_build_time` — API rejects it.

### GitLab Webhook (auto-deploy on push)

```bash
curl -sS -X POST "https://gitlab.com/api/v4/projects/$PROJECT_ID/hooks" \
  -H "PRIVATE-TOKEN: $GITLAB_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "url": "https://cool.example.com/webhooks/source/gitlab/events/manual",
    "token": "WEBHOOK_SECRET_FROM_APP",
    "push_events": true,
    "enable_ssl_verification": true
  }'
```

> **Critical:** The secret goes in the `"token"` field (sent as `X-Gitlab-Token` header), NEVER as `?secret=` query parameter in the URL. With secret in URL, Coolify returns 200 but does NOT trigger deploy.

### Deploy and Validate

```bash
# Trigger deploy
curl -sS -X POST "$COOLIFY_URL/applications/$APP_UUID/start" \
  -H "Authorization: Bearer $COOLIFY_KEY"

# Check status (~60s wait)
curl -sS -H "Authorization: Bearer $COOLIFY_KEY" \
  "$COOLIFY_URL/applications/$APP_UUID" | python3 -c "
import sys,json; d=json.load(sys.stdin); print(d['status'])"

# Verify HTTP response
curl -sS -o /dev/null -w "HTTP %{http_code}\n" https://mysite.com
```

---

## Astro Config for Coolify SSR

```typescript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import node from '@astrojs/node';

export default defineConfig({
  output: 'server', // or hybrid with per-page prerender
  adapter: node({ mode: 'standalone' }),
  server: { host: '0.0.0.0', port: 4321 },
});
```

For static output, no adapter needed — the Dockerfile handles nginx serving.

---

## Port Configuration

| Output Mode | Port | CMD |
|-------------|------|-----|
| SSR (Node adapter) | 4321 | `node dist/server/entry.mjs` |
| Static (nginx) | 80 | nginx default |
| Static (serve) | 80 | `npx serve dist -l 80 -s` |

Set `ports_exposes` in Coolify to match.

---

## Recommended Stack (Homelab-Tested)

Based on 17 production Astro sites:

```javascript
import seoGraph from '@jdevalk/astro-seo-graph/integration';
import agentmarkup from '@agentmarkup/astro';
import UnoCSS from '@unocss/astro';
import critters from 'astro-critters';
import compress from '@playform/compress';

// Key: compress() MUST be last integration
integrations: [mdx(), UnoCSS(), sitemap(), seoGraph(), agentmarkup(), critters(), compress()]
```

| Tool | Why |
|------|-----|
| UnoCSS > Tailwind | 5x faster build, smaller bundle |
| @playform/compress > astro-compress | Better maintained |
| astro-critters | Critical CSS inlining |
| @jdevalk/astro-seo-graph | All-in-one SEO (replaces astro-seo + robots-txt + indexnow) |
| @agentmarkup/astro | LLM visibility (llms.txt, markdown mirrors) |
| Plausible > GA4 | 1kb script, no cookie banner, self-hosted |
