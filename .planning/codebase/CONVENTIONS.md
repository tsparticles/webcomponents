# Coding Conventions

**Analysis Date:** 2026-04-10

## Naming Patterns

**Files:**
- Use PascalCase for core TypeScript class modules in component packages, as in `components/webcomponents/src/Particles.ts` and `components-legacy/webcomponents/src/Particles.ts`.
- Use lowercase kebab-case artifact names for distributed bundles and custom element tags, as in `components/webcomponents/build.ts` output `dist/web-particles.js` and `customElements.define("web-particles", Particles)` in `components/webcomponents/src/Particles.ts`.
- Use lowercase filenames for Node app entry files, as in `apps/webcomponents/app.js`.

**Functions:**
- Use camelCase for methods and local functions (`connectedCallback`, `notifyParticlesLoaded`, `rollupBuild`) in `components/webcomponents/src/Particles.ts` and `components/webcomponents/build.ts`.
- Use getter/setter pairs for public element API properties (`url`, `options`) in `components/webcomponents/src/Particles.ts`.

**Variables:**
- Use camelCase for locals (`options`, `port`, `rollupConfig`) in `components/webcomponents/src/Particles.ts`, `apps/webcomponents/app.js`, and `components/webcomponents/build.ts`.
- Use leading underscore for internal instance state (`_url`, `_options`) in `components/webcomponents/src/Particles.ts`.
- Use UPPER_SNAKE_CASE for script constants (`ENTRY_FILE`) in `components/webcomponents/build.ts`.

**Types:**
- Prefix interfaces with `I` for engine-provided option types (`ISourceOptions`) via imports in `components/webcomponents/src/Particles.ts`.
- Prefer explicit type annotations for API-facing and build-script functions (`notifyParticlesLoaded(container?: Container): void`, typed `rollupBuild` parameters/return in `components/webcomponents/build.ts`).

## Code Style

**Formatting:**
- Tool used: Prettier with shared config `@tsparticles/prettier-config`, declared in `components/webcomponents/package.json` and `components-legacy/webcomponents/package.json`.
- Key settings: No local `.prettierrc*` file detected in this repository root; formatting behavior is inherited from package-level `"prettier": "@tsparticles/prettier-config"` in `components/webcomponents/package.json`.
- Enforce formatting in CI/build steps using `prettier --check` scripts (`prettify:ci:src`, `prettify:ci:readme`) in `components/webcomponents/package.json` and `components-legacy/webcomponents/package.json`.

**Linting:**
- Tool used: ESLint dependencies are present in `apps/webcomponents/package.json` (`eslint`, `@typescript-eslint/parser`, `@typescript-eslint/eslint-plugin`, `eslint-config-prettier`).
- Key rules: No ESLint configuration file (`.eslintrc*`, `eslint.config.*`) detected in this repository, so no codified lint rule set is currently enforced from repo config files.

## Import Organization

**Order:**
1. Type imports first in TypeScript source where needed (`import type { ... }` in `components/webcomponents/src/Particles.ts`).
2. Runtime imports second (`import * as path from 'path'`, `import { ... } from 'rollup'` in `components/webcomponents/build.ts`).
3. CommonJS `require(...)` for Node runtime scripts (`apps/webcomponents/app.js`, plugin imports in `components/webcomponents/build.ts`).

**Path Aliases:**
- No path aliases detected; use package imports (for example `@tsparticles/engine` in `components/webcomponents/src/Particles.ts`) or relative paths where required.

## Error Handling

**Patterns:**
- Swallow-only parse guard is used for optional attribute JSON parsing:
  - `try { this._options = JSON.parse(options); } catch {}` in `components/webcomponents/src/Particles.ts` and `components-legacy/webcomponents/src/Particles.ts`.
- Promise rejection is handled at script boundary in build tooling:
  - `rollupBuild(rollupConfig).catch(err => { console.error(err); });` in `components/webcomponents/build.ts`.
- Early-return guard pattern is used for lifecycle checks:
  - `if (!this.isConnected) { return; }` in `components/webcomponents/src/Particles.ts`.

## Logging

**Framework:** console

**Patterns:**
- Use lightweight console diagnostics in scripts and demo app:
  - `console.log("Rollup warning: ", warning.message)` in `components/webcomponents/build.ts`.
  - `console.error(err)` in `components/webcomponents/build.ts`.
  - Startup status logging in `apps/webcomponents/app.js` via `console.log` inside `app.listen` callback.
- No centralized logger abstraction detected in `components/webcomponents/src/Particles.ts` or `apps/webcomponents/app.js`.

## Comments

**When to Comment:**
- Use short rationale comments to document non-obvious environment constraints, as shown by `// Safari requires https, probably a bug` in `apps/webcomponents/app.js`.
- Keep implementation mostly self-descriptive; source classes and build scripts contain minimal inline comments (`components/webcomponents/src/Particles.ts`, `components/webcomponents/build.ts`).

**JSDoc/TSDoc:**
- Not detected in implementation files (`components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`, `apps/webcomponents/app.js`).
- Documentation generation config exists (`components/webcomponents/typedoc.json`), so add TSDoc blocks for new exported APIs when introducing additional public surface.

## Function Design

**Size:**
- Functions and methods are kept compact and single-purpose in current source files (`connectedCallback`, `notifyParticlesLoaded` in `components/webcomponents/src/Particles.ts`; `rollupBuild` in `components/webcomponents/build.ts`).

**Parameters:**
- Prefer typed object parameters for multi-argument build functions (`rollupBuild({ inputOptions, outputOptions }: { ... })` in `components/webcomponents/build.ts`).
- Use optional parameters where values can be absent (`container?: Container` in `components/webcomponents/src/Particles.ts`).

**Return Values:**
- Use explicit return types for non-trivial functions in TypeScript build code (`Promise<RollupOutput>` in `components/webcomponents/build.ts`).
- Use void-returning methods for event dispatch side effects (`notifyParticlesLoaded(...): void` in `components/webcomponents/src/Particles.ts`).

## Module Design

**Exports:**
- Export named classes for reusable component APIs (`export class Particles extends HTMLElement` in `components/webcomponents/src/Particles.ts`).
- Keep side-effect registration at module bottom (`customElements.define(...)` in `components/webcomponents/src/Particles.ts`).

**Barrel Files:**
- Not detected in this repository; no `index.ts` export aggregators were found in `components/webcomponents/src/` or `components-legacy/webcomponents/src/`.

---

*Convention analysis: 2026-04-10*
