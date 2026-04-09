# V4 Target Architecture

Generated: 2026-04-09

## Objectives

- Align all component repositories with tsparticles v4 API line (`@tsparticles/*@4.x`).
- Keep compatibility with previous framework major versions where technically feasible.
- Standardize developer experience and release workflow across all wrappers.

## Unified Repository Layout (per framework repo)

- `components/<framework>/`: publishable wrapper package (`@tsparticles/<framework>`)
- `components/<addon>/`: optional integration wrappers (example: `@tsparticles/next`, `@tsparticles/nuxt`) when needed
- `apps/<framework-demo>/`: minimal demo app using local workspace package
- `apps/<framework-ssr-demo>/`: SSR/meta-framework demos where relevant (Next, Nuxt, Astro, etc.)
- `shared/` (optional): common test utilities, typed examples, fixture data

## Package Standards

- ESM-first output with preserved type declarations.
- Dual export support only when ecosystem constraints require it (Angular package format, WordPress bundling, legacy CJS consumers).
- Strict peerDependencies for host framework versions.
- `@tsparticles/engine` peer or dependency pinned to v4 line.
- Demo apps always consume `workspace:^` wrapper package.

## Wrapper API Contract (common)

- Declarative component with:
  - `id`
  - `options`
  - optional `url`
  - optional `particlesLoaded` callback
  - optional `particlesInit` callback
- Imperative escape hatch for engine/container access.
- No hidden global initialization side effects.

## Testing Standards

- Unit tests for wrapper lifecycle integration.
- One integration test per repo proving engine boot with v4 options.
- Type tests for public API surface where framework tooling supports it.

## Release and CI Standards

- Keep pnpm workspace in every repo.
- Build, lint, test commands normalized in root scripts.
- Use changesets (or consistent semver release tooling) for versioning + changelog generation.
- Publish pipeline verifies demo builds and wrapper package smoke tests.
