# Technology Stack

**Analysis Date:** 2026-04-10

## Languages

**Primary:**
- TypeScript (4.9.x toolchain) - Web Component library source and build scripts in `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`, and `components/webcomponents/build.ts`; compiler config in `components/webcomponents/tsconfig.json`; package versions in `components/webcomponents/package.json`.

**Secondary:**
- JavaScript (Node/CommonJS) - Demo server and app entrypoint in `apps/webcomponents/app.js`; npm scripts and monorepo orchestration in `package.json` and `apps/webcomponents/package.json`.
- Pug/Stylus templates - Demo UI build inputs in `apps/webcomponents/views/index.pug` and `apps/webcomponents/public/stylesheets/main.styl`.
- YAML/JSON configuration - Workspace/CI/tooling in `pnpm-workspace.yaml`, `.github/workflows/nodejs.yml`, `nx.json`, and `lerna.json`.

## Runtime

**Environment:**
- Node.js 20 in CI (`.github/workflows/nodejs.yml`).
- Browser runtime for the custom element (`customElements.define`) in `components/webcomponents/src/Particles.ts`.

**Package Manager:**
- pnpm (workspace root declares `pnpm@10.33.0`) in `package.json`.
- CI setup installs pnpm 8 in `.github/workflows/nodejs.yml`.
- Lockfile: present (`pnpm-lock.yaml`).

## Frameworks

**Core:**
- Native Web Components API - custom element implementation in `components/webcomponents/src/Particles.ts`.
- `@tsparticles/engine` (`^3.9.1`) - particle engine dependency used by the element in `components/webcomponents/package.json` and imported in `components/webcomponents/src/Particles.ts`.
- Express (`^4.18.2`) - demo web server in `apps/webcomponents/package.json` and `apps/webcomponents/app.js`.

**Testing:**
- Not detected (no Jest/Vitest/Mocha config or test command in `package.json`, `components/webcomponents/package.json`, and `apps/webcomponents/package.json`).

**Build/Dev:**
- Rollup (`^3.8.1`) - library bundling in `components/webcomponents/build.ts` and `components/webcomponents/package.json`.
- TypeScript (`^4.9.4`) - compile/transpile support in `components/webcomponents/package.json` and `apps/webcomponents/package.json`.
- ts-node (`^10.9.1`) - runs TypeScript build script via package scripts in `components/webcomponents/package.json`.
- Prettier + shared config (`@tsparticles/prettier-config`) - formatting checks/writes in `components/webcomponents/package.json`.
- Lerna + Nx - workspace orchestration in `package.json`, `lerna.json`, and `nx.json`.
- Pug CLI + Stylus - demo asset generation in `apps/webcomponents/package.json`.

## Key Dependencies

**Critical:**
- `@tsparticles/engine` (`^3.9.1`) - required for `window.tsParticles.load(...)` lifecycle in `components/webcomponents/src/Particles.ts`.
- `tsparticles` (`^3.9.1`, demo) - browser bundle served by demo app in `apps/webcomponents/app.js` and declared in `apps/webcomponents/package.json`.
- `@tsparticles/webcomponents` (`workspace:^`) - demo consumes workspace package in `apps/webcomponents/package.json`.

**Infrastructure:**
- `lerna` (`^6.3.0`) - multi-package build/version workflow in root `package.json` and `lerna.json`.
- `nx` (`^20.7.2`) - task runner/caching in root `package.json` and `nx.json`.
- `husky` + `@commitlint/*` - commit workflow tooling declared in root `package.json`.
- `@webcomponents/webcomponentsjs` (`^2.7.0`) - demo polyfill scripts loaded in `apps/webcomponents/views/index.pug`.

## Configuration

**Environment:**
- No runtime environment variables detected in source/config files (`components/**`, `apps/**`, `package.json`, `.github/workflows/nodejs.yml`).
- `.env` files: Not detected in repository root/workspace scan.
- Required runtime setup is package installation + static assets (no secret-based bootstrap paths detected).

**Build:**
- Monorepo/workspaces: `package.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json`.
- Library bundling/transpile: `components/webcomponents/build.ts`, `components/webcomponents/tsconfig.json`.
- Demo build: scripts in `apps/webcomponents/package.json`; template source `apps/webcomponents/views/index.pug`.
- CI build pipeline: `.github/workflows/nodejs.yml`.

## Platform Requirements

**Development:**
- Node.js + pnpm workspace support required to run root scripts in `package.json`.
- TypeScript/Rollup toolchain required for package build in `components/webcomponents/package.json`.

**Production:**
- Library publish target: npm package artifacts from `dist/web-particles.js` configured in `components/webcomponents/package.json` and produced by `components/webcomponents/build.ts`.
- Demo deployment target: Node/Express process serving static files from `apps/webcomponents/app.js`.

---

*Stack analysis: 2026-04-10*
