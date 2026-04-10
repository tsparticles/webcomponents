# Codebase Concerns

**Analysis Date:** 2026-04-10

## Tech Debt

**Duplicated package implementation (`components` vs `components-legacy`):**
- Issue: The same Web Component source is duplicated in two packages, creating parallel maintenance and release work.
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`, `components/webcomponents/package.json`, `components-legacy/webcomponents/package.json`
- Impact: Bug fixes and behavior changes can drift between packages; reviewers must validate changes twice.
- Fix approach: Keep one canonical source package and generate/alias legacy distribution metadata from that source.

**Build tool dependency mismatch:**
- Issue: Build script imports `@rollup/plugin-typescript` and `@rollup/plugin-node-resolve`, but package manifests declare legacy `rollup-plugin-typescript2` and deprecated `rollup-plugin-node-resolve`.
- Files: `components/webcomponents/build.ts`, `components/webcomponents/package.json`, `components-legacy/webcomponents/package.json`, `pnpm-lock.yaml`
- Impact: Fresh installs can fail or behave inconsistently across environments; upgrades are harder because manifests do not represent actual runtime build requirements.
- Fix approach: Align `package.json` dependencies with `build.ts` imports and remove deprecated plugin packages.

**Monorepo orchestration overlap without clear ownership:**
- Issue: Both Lerna and Nx are configured for build orchestration at the same time.
- Files: `package.json`, `lerna.json`, `nx.json`
- Impact: CI and local builds can diverge depending on which command is used, increasing maintenance overhead.
- Fix approach: Define one primary orchestrator for CI/release paths and keep the other optional or remove it.

## Known Bugs

**Duplicate custom element registration can throw at runtime:**
- Symptoms: `DOMException` for duplicate registration (`Failed to execute 'define' on 'CustomElementRegistry'`) if more than one package defining `web-particles` is loaded.
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`
- Trigger: Load/import both `@tsparticles/webcomponents` and `web-particles` packages in the same page/runtime.
- Workaround: Ensure only one package is loaded; add `if (!customElements.get("web-particles"))` guard before `customElements.define`.

**Silent options parse failure hides invalid config bugs:**
- Symptoms: Component does not initialize from `options` attribute and emits no actionable error.
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`
- Trigger: Invalid JSON in `options` HTML attribute.
- Workaround: Validate JSON before passing `options`; add explicit error event/log in catch branch.

## Security Considerations

**HTTP hardening disabled in demo server:**
- Risk: Security headers are not applied because `helmet` middleware is commented out.
- Files: `apps/webcomponents/app.js`
- Current mitigation: None in code; comment indicates browser compatibility issue was used to bypass middleware.
- Recommendations: Re-enable `helmet` with targeted policy exceptions instead of global disable.

**Broad static exposure of dependency directories:**
- Risk: Demo server publicly serves entire `node_modules` subtrees (`/tsparticles`, `/webcomponentsjs`, etc.), expanding attack surface and accidental file exposure risk.
- Files: `apps/webcomponents/app.js`
- Current mitigation: Demo-only app context.
- Recommendations: Serve only required built artifacts (allowlist specific files), keep directory listing disabled, and pin static mount paths to minimal bundles.

## Performance Bottlenecks

**Container re-initialization on each property set:**
- Problem: Setting `url` or `options` always destroys current container and reloads engine config.
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`
- Cause: No debounce/equality check before re-creating particle container.
- Improvement path: Skip reload when incoming values are unchanged; throttle frequent updates; provide explicit `refresh()` API for forced reloads.

**Missing teardown on element removal:**
- Problem: No `disconnectedCallback` to destroy active container when element leaves DOM.
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`
- Cause: Lifecycle cleanup hook is not implemented.
- Improvement path: Add `disconnectedCallback()` with `this.container.current?.destroy()` and clear references.

## Fragile Areas

**Global singleton dependency with no guard:**
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`, `apps/webcomponents/views/index.pug`
- Why fragile: Component assumes `window.tsParticles` exists; missing/late script load causes runtime failure.
- Safe modification: Add readiness checks before calling `window.tsParticles.load` and emit structured error events when engine is unavailable.
- Test coverage: No automated tests detected for script-load ordering or missing-engine scenarios.

**Generated artifact drift risk in local builds:**
- Files: `components/webcomponents/src/Particles.ts`, `components/webcomponents/dist/web-particles.js`, `.gitignore`
- Why fragile: Built output present in workspace can diverge from source behavior (`load` in source vs `set/setJSON` in generated file), confusing manual verification.
- Safe modification: Rebuild before local verification and ensure generated artifacts are treated as ephemeral output.
- Test coverage: No artifact consistency check in CI.

## Scaling Limits

**Single global engine access model (`window.tsParticles`):**
- Current capacity: Works for simple browser pages where engine script is loaded globally.
- Limit: Breaks in stricter module isolation, SSR/worker contexts, and multi-runtime embedding where global window state is unavailable or delayed.
- Scaling path: Support explicit engine injection API and keep global fallback only for backward compatibility.

**Single fixed custom element name:**
- Current capacity: One component contract (`web-particles`) per page ecosystem.
- Limit: Integration conflicts when multiple wrappers/versions coexist in micro-frontend or federated deployments.
- Scaling path: Centralize registration guard and optionally expose configurable tag registration helper.

## Dependencies at Risk

**`rollup-plugin-node-resolve@5.2.0` (deprecated):**
- Risk: Deprecated package already flagged in lockfile; maintenance and compatibility risk increases over time.
- Impact: Build pipeline fragility and blocked dependency modernization.
- Migration plan: Remove deprecated package and standardize on `@rollup/plugin-node-resolve` in manifests and lockfile.

**Toolchain version drift across workspace/CI:**
- Risk: Root `packageManager` specifies pnpm 10, while workflow installs pnpm 8.
- Impact: Lockfile/install behavior can differ between local and CI runs.
- Migration plan: Pin the same pnpm major version in `.github/workflows/nodejs.yml` as declared in root `package.json`.

## Missing Critical Features

**No automated test suite for component runtime behaviors:**
- Problem: Core lifecycle behaviors (`connectedCallback`, setter reloads, custom event dispatch, cleanup) have no test coverage.
- Blocks: Safe refactoring of component lifecycle and reliability of migration work.

**No dedicated lint/typecheck/test quality gates at root:**
- Problem: Root scripts expose build orchestration only (`build`, `build:ci`, `build:lerna`, `build:nx`) and no explicit `test`/`lint` workflow gate.
- Blocks: Early detection of regressions and enforceable quality baseline for contributors.

## Test Coverage Gaps

**Web component lifecycle edge cases are untested:**
- What's not tested: Duplicate registration guard, invalid `options` JSON handling, missing `window.tsParticles`, and DOM detach cleanup.
- Files: `components/webcomponents/src/Particles.ts`, `components-legacy/webcomponents/src/Particles.ts`
- Risk: Runtime failures appear in consumer apps without pre-release detection.
- Priority: High

**Demo server security behavior is untested:**
- What's not tested: Helmet/header behavior, static path exposure boundaries, and startup behavior under production hardening.
- Files: `apps/webcomponents/app.js`
- Risk: Insecure defaults can persist unnoticed and be copied into production-like deployments.
- Priority: Medium

---

*Concerns audit: 2026-04-10*
