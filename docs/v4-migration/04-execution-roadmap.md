# V4 Execution Roadmap

Generated: 2026-04-09

## Phase 0 - Foundation and Tooling Alignment

1. Normalize root scripts in each repo: `build`, `lint`, `test`, `typecheck`, `demo`.
2. Align tsconfig, eslint, prettier, vitest/jest strategy.
3. Enforce workspace dependency linking (`workspace:^`) for local wrapper usage in demos.
4. Add/refresh CI workflows with same quality gates.

Done when:

- Every repo can run `pnpm build`, `pnpm test`, `pnpm lint` successfully.
- Demo app(s) boot locally per repo.

## Phase 1 - Core Wrapper Rewrite Template

1. Choose one pilot wrapper with broad impact: `@tsparticles/react`.
2. Rewrite wrapper internals against tsparticles v4 APIs.
3. Add compatibility shim strategy for React 16.8+ to 19.
4. Add integration tests and migration notes.

Done when:

- Pilot package publishes cleanly from local workflow.
- Demo app validates runtime behavior and callbacks.
- Pattern is reusable for all other wrappers.

## Phase 2 - High-Impact Framework Waves

Wave A:

1. React
2. Angular
3. Vue (unified package POC)

Wave B:

1. Svelte
2. Solid
3. Preact
4. Lit

Wave C:

1. Astro
2. Qwik
3. Web Components
4. WordPress

Wave D:

1. Ember
2. Riot
3. Inferno
4. jQuery

Done when:

- Each wave has green build/lint/test + demo smoke test.
- Wrapper API docs updated per framework repo.

## Phase 3 - Vue Consolidation Outcome

1. Evaluate unified `vue` repo performance and maintenance complexity.
2. If successful, deprecate `vue2` and `vue3` with clear migration docs.
3. If not successful, keep split wrappers but share implementation core.

Done when:

- One documented final Vue strategy is selected and applied.

## Phase 4 - Final Hardening and Release

1. Run cross-repo verification matrix.
2. Execute beta release round for all wrappers.
3. Collect issues and patch.
4. Tag stable releases.

Done when:

- All target wrappers publish stable v4-compatible versions.
- Demos and package READMEs are consistent.

## Risk Register

- Angular version spread may force support floor increase.
- Vue dual-major support could increase maintenance burden significantly.
- Older framework support may require separate transpilation/build outputs.
- WordPress packaging constraints may diverge from standard wrapper pipeline.

## Suggested Order for Immediate Hands-on Work

1. React pilot rewrite.
2. Angular feasibility spike.
3. Vue dual-major proof of concept.
4. Apply generated template to remaining wrappers.
