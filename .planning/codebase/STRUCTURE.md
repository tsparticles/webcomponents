# Codebase Structure

**Analysis Date:** 2026-04-10

## Directory Layout

```text
webcomponents/
├── apps/                     # Runnable demo applications
│   └── webcomponents/        # Express + Pug demo host for the web component
├── components/               # Current publishable packages
│   └── webcomponents/        # Main `@tsparticles/webcomponents` package
├── components-legacy/        # Legacy package namespace kept in workspace
│   └── webcomponents/        # Legacy `web-particles` package mirror
├── docs/                     # Migration and architecture planning notes
├── .github/workflows/        # CI workflows
├── .planning/codebase/       # Generated architecture/quality/stack mapping docs
├── package.json              # Root workspace scripts and tooling deps
├── pnpm-workspace.yaml       # Workspace package boundaries
├── lerna.json                # Lerna package orchestration config
└── nx.json                   # Nx task defaults and caching config
```

## Directory Purposes

**`apps/`:**
- Purpose: Host executable demos that consume workspace packages.
- Contains: Application bootstrap, templates, demo static assets, local preset configs.
- Key files: `apps/webcomponents/app.js`, `apps/webcomponents/views/index.pug`, `apps/webcomponents/public/presets/default.json`, `apps/webcomponents/package.json`

**`components/`:**
- Purpose: Store the primary, publishable wrapper package implementation.
- Contains: TypeScript source, package-level build script, package manifest, generated dist artifact.
- Key files: `components/webcomponents/src/Particles.ts`, `components/webcomponents/build.ts`, `components/webcomponents/package.json`, `components/webcomponents/dist/web-particles.js`

**`components-legacy/`:**
- Purpose: Preserve legacy package path and naming while keeping equivalent implementation.
- Contains: Legacy package manifest and source mirror.
- Key files: `components-legacy/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/package.json`

**`docs/`:**
- Purpose: Capture migration strategy and architecture planning material.
- Contains: V4 migration analyses and execution plans.
- Key files: `docs/v4-migration/02-target-architecture.md`, `docs/v4-migration/01-audit-summary.md`, `docs/v4-migration/04-execution-roadmap.md`

**`.planning/codebase/`:**
- Purpose: Persist machine-consumable codebase maps for planning/execution workflows.
- Contains: Generated markdown guides (`ARCHITECTURE.md`, `STRUCTURE.md`, etc.).
- Key files: `.planning/codebase/ARCHITECTURE.md`, `.planning/codebase/STRUCTURE.md`

## Key File Locations

**Entry Points:**
- `components/webcomponents/src/Particles.ts`: Runtime entry for custom element definition and tsParticles lifecycle.
- `apps/webcomponents/app.js`: Demo server process entry.
- `package.json`: Workspace-level build orchestration entry.

**Configuration:**
- `pnpm-workspace.yaml`: Defines workspace package globs (`apps/*`, `components/*`, `components-legacy/*`).
- `lerna.json`: Defines package orchestration and versioning workflow.
- `nx.json`: Defines Nx named inputs and build target caching.
- `components/webcomponents/tsconfig.json`: TypeScript compiler settings for component package.

**Core Logic:**
- `components/webcomponents/src/Particles.ts`: Main component logic.
- `components/webcomponents/build.ts`: Bundle generation logic.
- `components-legacy/webcomponents/src/Particles.ts`: Legacy mirror of component logic.

**Testing:**
- Not detected. No `*.test.*`/`*.spec.*` files or test runner config files were found in the workspace root package structure.

## Naming Conventions

**Files:**
- Component class files use PascalCase: `components/webcomponents/src/Particles.ts`.
- Package configuration files use conventional lowercase names: `package.json`, `tsconfig.json`, `typedoc.json`.
- Demo template and static assets use lowercase names: `apps/webcomponents/views/index.pug`, `apps/webcomponents/public/presets/default.json`.

**Directories:**
- Top-level workspace directories are lowercase plural domains: `apps/`, `components/`, `components-legacy/`.
- Package directories follow kebab-case package identity: `apps/webcomponents/`, `components/webcomponents/`.

## Where to Add New Code

**New Feature:**
- Primary code: Add component runtime behavior in `components/webcomponents/src/` (extend `Particles.ts` or add adjacent modules and import from `Particles.ts`).
- Tests: Not applicable in current structure; add a new test directory under `components/webcomponents/` (for example `components/webcomponents/test/`) and introduce runner config at package or root level.

**New Component/Module:**
- Implementation: Place publishable wrapper code under `components/<new-package>/` with its own `package.json`, `build.ts`/build config, and `src/` entry mirroring `components/webcomponents/`.

**Utilities:**
- Shared helpers: Create package-scoped helper modules under `components/webcomponents/src/` for reuse within the wrapper.
- Cross-package helpers: Add a new shared workspace package (for example `components/shared-utils/`) and consume via `workspace:^` references.

## Special Directories

**`components/webcomponents/dist/`:**
- Purpose: Built distributable output (`web-particles.js` and sourcemap).
- Generated: Yes
- Committed: Yes (present in repository at `components/webcomponents/dist/web-particles.js`).

**`node_modules/`:**
- Purpose: Installed dependencies for root and workspace packages.
- Generated: Yes
- Committed: No (ignored by `.gitignore`).

**`docs/v4-migration/`:**
- Purpose: Migration planning artifacts for v4 alignment.
- Generated: No
- Committed: Yes

---

*Structure analysis: 2026-04-10*
