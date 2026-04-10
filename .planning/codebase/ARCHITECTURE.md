# Architecture

**Analysis Date:** 2026-04-10

## Pattern Overview

**Overall:** Monorepo workspace with package-oriented architecture (publishable wrapper package + demo application), orchestrated by pnpm workspaces with Lerna/Nx task runners.

**Key Characteristics:**
- Single-purpose wrapper module centered on a custom element in `components/webcomponents/src/Particles.ts`.
- Side-effect registration pattern (`customElements.define`) at module load time in `components/webcomponents/src/Particles.ts`.
- Clear separation between library packaging (`components/webcomponents/*`) and runnable demo host (`apps/webcomponents/*`).

## Layers

**Workspace Orchestration Layer:**
- Purpose: Coordinate builds across all packages in the repository.
- Location: `package.json`, `pnpm-workspace.yaml`, `lerna.json`, `nx.json`
- Contains: Workspace definitions, root scripts, task defaults, package globs.
- Depends on: pnpm workspace resolution and Lerna/Nx executors configured in `package.json`.
- Used by: All package-level build commands in `apps/*`, `components/*`, and `components-legacy/*`.

**Component Runtime Layer:**
- Purpose: Implement browser runtime behavior for the `<web-particles>` custom element.
- Location: `components/webcomponents/src/Particles.ts` (also mirrored in `components-legacy/webcomponents/src/Particles.ts`).
- Contains: HTMLElement subclass, attribute parsing (`options`, `url`), tsParticles container lifecycle, custom events.
- Depends on: `window.tsParticles` global engine object and types from `@tsparticles/engine`.
- Used by: Browser pages importing built output `components/webcomponents/dist/web-particles.js`.

**Packaging/Build Layer:**
- Purpose: Transform TypeScript source into distributable ESM output with sourcemaps.
- Location: `components/webcomponents/build.ts`, `components/webcomponents/tsconfig.json`, `components/webcomponents/package.json`
- Contains: Rollup input/output config, TypeScript plugin wiring, package scripts (`build`, `build:ci`, `prepack`).
- Depends on: Rollup + TypeScript toolchain configured in `components/webcomponents/package.json`.
- Used by: Root build orchestration via `package.json` scripts and direct package builds.

**Demo Host Layer:**
- Purpose: Serve a runnable integration environment for the wrapper package.
- Location: `apps/webcomponents/app.js`, `apps/webcomponents/views/index.pug`, `apps/webcomponents/public/*`
- Contains: Express app setup, static asset mounting, Pug template, preset JSON.
- Depends on: Express, stylus middleware, demo dependencies declared in `apps/webcomponents/package.json`.
- Used by: Local demo startup via `apps/webcomponents/package.json` script `start`.

## Data Flow

**Custom Element Initialization Flow:**

1. Browser loads `/web-particles/web-particles.js` from the demo page in `apps/webcomponents/views/index.pug`, triggering module evaluation and `customElements.define("web-particles", Particles)` in `components/webcomponents/src/Particles.ts`.
2. Element instance constructor reads HTML attributes using `getAttribute("options")` and `getAttribute("url")` in `components/webcomponents/src/Particles.ts`, stores parsed state, and emits `particlesInit`.
3. On `connectedCallback`, runtime selects either URL-based or inline-options load path and calls `window.tsParticles.load(...)` in `components/webcomponents/src/Particles.ts`.
4. Loaded container is stored in `container.current`, and `particlesLoaded` event is emitted in `components/webcomponents/src/Particles.ts`.

**Attribute Update Flow:**

1. Consumer code sets `element.url` or `element.options` on the `Particles` instance in `components/webcomponents/src/Particles.ts`.
2. Existing container is torn down via `this.container.current?.destroy()`.
3. New container is created through `window.tsParticles.load(...)` and surfaced through `particlesLoaded`.

**State Management:**
- State is local and instance-bound in private fields (`_url`, `_options`) plus a mutable holder object (`container.current`) in `components/webcomponents/src/Particles.ts`.
- No shared store, reducer, or centralized state manager is present.

## Key Abstractions

**Custom Element Wrapper (`Particles`):**
- Purpose: Bridge declarative HTML usage to imperative tsParticles engine initialization.
- Examples: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`
- Pattern: Thin adapter abstraction around third-party engine load/destroy lifecycle.

**Container Handle Abstraction:**
- Purpose: Retain and replace the current tsParticles container safely across updates.
- Examples: `container.current` usage in `components/webcomponents/src/Particles.ts`
- Pattern: Mutable reference object (single-slot handle) with optional chaining for safe teardown.

**Build Configuration Abstraction:**
- Purpose: Encapsulate bundle settings independent from runtime class logic.
- Examples: `rollupConfig` and `rollupBuild` in `components/webcomponents/build.ts`
- Pattern: Scripted build pipeline with typed `InputOptions`/`OutputOptions` definitions.

## Entry Points

**Workspace Build Entry Point:**
- Location: `package.json`
- Triggers: `pnpm run build`, `pnpm run build:ci`, `pnpm run build:lerna`, `pnpm run build:nx`
- Responsibilities: Dispatch build tasks across workspace packages.

**Component Package Build Entry Point:**
- Location: `components/webcomponents/package.json` (`build`, `build:ci`, `prepack`)
- Triggers: Root orchestrator tasks and direct package build calls.
- Responsibilities: Format-check/write source and README, then execute `build.ts` to emit `dist/web-particles.js`.

**Component Runtime Entry Point:**
- Location: `components/webcomponents/src/Particles.ts`
- Triggers: Browser module import of built artifact.
- Responsibilities: Register custom element, parse element attributes, start and recycle tsParticles containers, emit integration events.

**Demo Application Entry Point:**
- Location: `apps/webcomponents/app.js`
- Triggers: `pnpm run start` in `apps/webcomponents/package.json`.
- Responsibilities: Initialize Express, serve static dependencies, render `views/index.pug`, listen on port `3005`.

## Error Handling

**Strategy:** Localized defensive checks with minimal explicit recovery.

**Patterns:**
- Safe teardown using optional chaining (`this.container.current?.destroy()`) in `components/webcomponents/src/Particles.ts`.
- Silent JSON parse fallback (`try { JSON.parse(...) } catch {}`) in `components/webcomponents/src/Particles.ts`.
- Promise rejections in build path bubbled to `console.error` in `components/webcomponents/build.ts`.

## Cross-Cutting Concerns

**Logging:** Console-based logging only (`console.log` in `apps/webcomponents/app.js`, Rollup warning logging in `components/webcomponents/build.ts`).
**Validation:** Attribute-to-object conversion validation is limited to JSON parse try/catch in `components/webcomponents/src/Particles.ts`; schema validation is not present.
**Authentication:** Not applicable for component runtime and demo host; no auth middleware or identity layer detected in `apps/webcomponents/app.js`.

---

*Architecture analysis: 2026-04-10*
