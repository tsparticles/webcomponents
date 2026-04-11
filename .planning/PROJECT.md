# web-particles

## What This Is

`web-particles` is the official tsParticles Web Component package for adding configurable particle effects to web pages through a declarative `<web-particles>` custom element. It serves frontend developers who want lightweight integration via HTML attributes (`url`, `options`) without directly wiring tsParticles engine internals. This repository also includes a demo app and monorepo build orchestration to develop, validate, and publish the component.

## Core Value

Developers can drop in `<web-particles>` and reliably get tsParticles effects running with minimal integration effort.

## Requirements

### Validated

- ✓ Consumers can install the package from npm and use it as an official tsParticles Web Component — existing
- ✓ Consumers can initialize particles via `url` or inline `options` attributes on `<web-particles>` — existing
- ✓ The custom element can be styled directly with standard HTML attributes/class/style — existing
- ✓ Repository supports workspace builds with pnpm/Lerna/Nx orchestration — existing
- ✓ Demo app can host and render the component in a browser context — existing

### Active

- [ ] Keep the web component aligned with current tsParticles engine versions and ecosystem expectations
- [ ] Maintain a clear v1 requirements baseline for future phase planning and execution
- [ ] Preserve a contributor-friendly workflow across component package, demo app, and workspace tooling

### Out of Scope

- Building a full visual editor for particle configs — not required for the core wrapper value
- Replacing tsParticles engine internals — this project is a wrapper/integration layer, not a renderer rewrite

## Context

- This is a brownfield monorepo with existing runtime code under `components/webcomponents/`, demo host code under `apps/webcomponents/`, and legacy mirror package code under `components-legacy/`.
- A codebase map exists in `.planning/codebase/` with architecture, stack, integrations, conventions, testing, and concerns analysis completed.
- The README indicates the primary user-facing capabilities today: install package, configure via HTML, style container directly, and use supported build orchestrators.
- Existing architecture relies on Web Components + tsParticles with Express/Pug demo infrastructure and TypeScript + Rollup build tooling.

## Constraints

- **Tech stack**: Keep compatibility with current monorepo toolchain (`pnpm`, `lerna`, `nx`, TypeScript/Rollup) — avoids destabilizing existing release/build flow
- **Backward compatibility**: Preserve existing `<web-particles>` attribute-based usage (`url`, `options`) — protects current adopters
- **Scope**: Prioritize wrapper reliability and maintainability over net-new product surface — keeps focus on core value
- **Repository context**: Plan as evolution of an existing codebase, not a greenfield rewrite — leverages validated architecture and docs

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Initialize from existing repository README plus codebase map | Auto mode provided README as idea source and codebase map as current-state evidence | ✓ Good |
| Treat project as brownfield with inferred validated capabilities | Existing package/demo/build flows are already implemented and in use | ✓ Good |
| Use auto workflow defaults with quick depth and parallel planning | Fast initialization while preserving research/checking/verifier safeguards | — Pending |

---
*Last updated: 2026-04-10 after initialization*
