# Wave A Implementation Backlog

Generated: 2026-04-09

## Scope

- Angular
- Vue (unified repo first)
- React hardening follow-up

## React Follow-up Tasks

- Fix warning in `apps/nextjs/pages/index.js` (`useEffect` dependency array).
- Validate `apps/nextjs-beta` build with v4 deps.
- Add integration test covering `particlesInit` and `particlesLoaded` callbacks.
- Decide final publish version for `@tsparticles/react` (`4.0.0-beta.x`).

## Angular Tasks

- Migrate `components/particles` wrapper API to v4 engine callbacks.
- Validate Angular standalone component support as default.
- Test compatibility floor candidate:
  - Angular 17+
- Keep best-effort support notes for Angular 16 if build constraints permit.
- Update demo apps (`angular-demo`, `ionic-demo`) to v4 deps.

## Vue Tasks

- In `vue` repo, implement dual adapter architecture:
  - Vue 3 runtime path
  - Vue 2.7 compatibility path
- Create strict demo validation:
  - `apps/vue3`
  - `apps/vue2`
- Add explicit decision gate metrics:
  - build complexity
  - runtime parity
  - maintenance overhead
- If metrics fail threshold, keep split maintenance in `vue2` and `vue3` repos temporarily.

## Release Criteria for Wave A

- Wrapper package build passes.
- At least one demo app per repo builds and runs with tsparticles v4 deps.
- README migration notes include host framework compatibility ranges.
