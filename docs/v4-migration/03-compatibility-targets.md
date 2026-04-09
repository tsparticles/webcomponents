# Compatibility Targets (Draft)

Generated: 2026-04-09

This matrix defines target compatibility windows for wrapper rewrites against tsparticles v4.

## Principles

- Prefer broad host-framework compatibility when API/lifecycle differences are manageable.
- Do not compromise runtime correctness to support very old framework versions.
- If compatibility requires large code branching, split packages by major framework line.

## Proposed Targets

| Wrapper | Primary target | Secondary compatible targets | Notes |
|---|---|---|---|
| `@tsparticles/react` | React 19 | React 18, 17, 16.8+ | Use hooks-only API with fallback-safe behavior; keep JSX runtime compatibility. |
| `@tsparticles/preact` | Preact 10+ | Preact X variants | Keep lightweight API similar to React wrapper. |
| `@tsparticles/angular` | Angular 19/20 | Angular 17/18 (best effort) | Needs standalone-first API and zone-aware init strategy. |
| `@tsparticles/vue` | Vue 3.4+ | Vue 2.7 via compatibility bridge (if maintainable) | Decide after POC; if unstable keep dedicated `vue2` package temporarily. |
| `@tsparticles/svelte` | Svelte 5 | Svelte 4 (if build chain allows) | Keep action/component split if useful. |
| `@tsparticles/solid` | Solid 1.x latest | none declared initially | Expand only if demand emerges. |
| `@tsparticles/lit` | Lit 3 | Lit 2 (if no API conflict) | Web component wrapper should remain framework-neutral. |
| `@tsparticles/webcomponents` | Native custom elements latest | broad browser compatibility via transpilation | Validate with modern + evergreen browsers. |
| `@tsparticles/astro` | Astro 5 | Astro 4 (best effort) | Client directive docs and SSR boundaries required. |
| `@tsparticles/qwik` | Qwik latest stable | none declared initially | Ensure resumability-safe initialization. |
| `@tsparticles/ember` | Ember latest LTS | previous LTS | Needs modern addon packaging update. |
| `@tsparticles/riot` | Riot latest | previous minor | Validate lifecycle cleanup behavior. |
| `@tsparticles/inferno` | Inferno latest | none declared initially | Keep parity with React API shape where possible. |
| `@tsparticles/jquery` | jQuery 3.7+ | jQuery 3.5+ | Wrapper plugin API modernization only. |
| `@tsparticles/wordpress` | Current WP stable | last 2 WP major versions | Gutenberg + shortcode fallback strategy. |

## Vue Consolidation Decision Gate

- Gate A: Build one `@tsparticles/vue` package with dual build outputs and adapter layer.
- Gate B: Validate with demo apps for Vue 2.7 and Vue 3.x.
- If both demos are green and maintenance burden is acceptable, start deprecation plan for `vue2` and `vue3` repos.
- If not green, keep split repos and share core wrapper logic through generated common module.
