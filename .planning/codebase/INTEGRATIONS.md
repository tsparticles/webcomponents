# External Integrations

**Analysis Date:** 2026-04-10

## APIs & External Services

**Package Registries & Distribution:**
- npm registry - package distribution for `web-particles` and `@tsparticles/webcomponents` via package metadata in `components-legacy/webcomponents/package.json` and `components/webcomponents/package.json`.
  - SDK/Client: npm/pnpm CLI via scripts in root `package.json` and package manifests.
  - Auth: Not defined in repository files (expected local/CI npm credentials outside tracked files).

**tsParticles ecosystem dependency:**
- tsParticles engine/runtime packages - integration with `@tsparticles/engine` and `tsparticles` in `components/webcomponents/package.json`, `components-legacy/webcomponents/package.json`, and `apps/webcomponents/package.json`; runtime usage in `components/webcomponents/src/Particles.ts`.
  - SDK/Client: `@tsparticles/engine`, `tsparticles`.
  - Auth: Not applicable.

**Source control & sponsorship links (metadata only):**
- GitHub repository/issues/sponsors URLs in `components/webcomponents/package.json`, `components-legacy/webcomponents/package.json`, and `README.md`.
  - SDK/Client: Not applicable.
  - Auth: Not applicable.

## Data Storage

**Databases:**
- Not detected.
  - Connection: Not applicable.
  - Client: Not applicable.

**File Storage:**
- Local filesystem only (Express static serving from `./public` and `./node_modules` in `apps/webcomponents/app.js`).

**Caching:**
- None detected (no Redis/memcached/cache provider integrations in `apps/**`, `components/**`, or root config files).

## Authentication & Identity

**Auth Provider:**
- None detected.
  - Implementation: Not applicable.

## Monitoring & Observability

**Error Tracking:**
- None detected (no Sentry/Bugsnag/Rollbar integrations in scanned source/config files).

**Logs:**
- Basic console logging only in demo server startup (`console.log`) at `apps/webcomponents/app.js`.

## CI/CD & Deployment

**Hosting:**
- Demo runtime is self-hosted Node/Express (`apps/webcomponents/app.js`).
- Package artifacts intended for npm distribution via package metadata and prepack build scripts in `components/webcomponents/package.json` and `components-legacy/webcomponents/package.json`.

**CI Pipeline:**
- GitHub Actions workflow in `.github/workflows/nodejs.yml`.
  - Runs on pushes/PRs to `main`, `dev`, `legacy`.
  - Executes dependency install and `pnpm run build:ci` from root `package.json`.

## Environment Configuration

**Required env vars:**
- Not detected (no `process.env.*` / `import.meta.env.*` usage found in scanned source/config files).

**Secrets location:**
- Not defined in repository tracked files.
- `.env` files: Not detected in repository scan.

## Webhooks & Callbacks

**Incoming:**
- None detected (no webhook endpoint routes in `apps/webcomponents/app.js` or other source files).

**Outgoing:**
- None detected (no HTTP client usage such as `fetch`/`axios` in scanned source files).

---

*Integration audit: 2026-04-10*
