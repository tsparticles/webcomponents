# V4 Migration Audit Summary

Generated: 2026-04-09

Reference baseline: tsparticles monorepo is at `4.0.0-beta.11` for `@tsparticles/*` packages.

## Repository Snapshot

```text

Repo          Packages Components Apps HasTsParticlesV2Deps HasTsParticlesV3Deps
----          -------- ---------- ---- -------------------- --------------------
angular              9          6    2                 True                 True
astro                3          1    1                False                 True
ember                2          1    0                False                 True
inferno              3          1    1                False                 True
jquery               3          1    1                 True                 True
lit                  3          1    1                False                False
preact               3          1    1                 True                 True
qwik                 1          0    0                False                False
react                8          1    4                False                 True
riot                 4          1    1                False                 True
solid                3          1    1                 True                 True
svelte               4          1    2                False                 True
vue                  6          1    4                 True                 True
vue2                 4          1    2                False                 True
vue3                 4          1    2                 True                 True
webcomponents        4          1    1                False                 True
wordpress            1          0    0                False                 True
```

## Key Findings

- All component repos are on pnpm 10 and mostly already split as workspace with `apps/*` and `components/*`.
- Most wrappers still target `@tsparticles/*` v3 ranges; some wrappers are still on old v2 package versions.
- Structure is inconsistent across repos (build scripts, test setup, demo app quality, output format).
- Vue support is fragmented across 3 repositories (`vue`, `vue2`, `vue3`) and needs convergence strategy.

## Immediate Direction

- Keep `tsparticles` repo untouched (source of truth/reference only).
- Standardize each component repo to the same monorepo topology: `components/*` for publishable wrappers and `apps/*` for demos/integration sandboxes.
- Upgrade wrappers to target core `@tsparticles/engine` and bundle packages at v4 line while defining compatibility windows on host frameworks.
- Implement one shared migration template and apply repo-by-repo in waves.
