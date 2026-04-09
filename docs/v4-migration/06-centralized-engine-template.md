# Centralized Engine Init Pattern — Template for All Frameworks

## Core Principle

**Load tsParticles plugins ONCE at application bootstrap, not for every component instance.**

This document provides a framework-agnostic template. Adapt to your framework's patterns (DI, Context, Store, etc.).

## Architecture

```
App Start
  ↓
Init Container (Provider/Service/Plugin/Store)
  ├─ Load engine
  ├─ Load all plugins/presets
  └─ Cache engine instance
  ↓
Components Query Cached Engine
  ├─ Particles 1
  ├─ Particles 2
  └─ Particles N
  ↓
Each Instance Only:
  └─ Load own particle config
     (engine + plugins already ready)
```

## Benefits

| Aspect | Benefit |
|--------|---------|
| **Performance** | Plugins init once; 1-2 seconds saved on first load, 0ms overhead for subsequent components |
| **Memory** | Shared engine instance; plugins not duplicated across N components |
| **UX** | First component appears faster; others spawn instantly |
| **DX** | Clear separation: app-level bootstrap vs. component-level config |

## Implementation Patterns by Framework

### React — **Context + Provider Hook**

```tsx
// ParticlesProvider.tsx
const ParticlesContext = React.createContext<{ engine?: Engine }>(undefined);

export const ParticlesProvider = ({ children, particlesInit }) => {
  const [engine, setEngine] = useState();

  useEffect(() => {
    (async () => {
      const { tsParticles } = await import("@tsparticles/engine");
      await particlesInit?.(tsParticles);
      setEngine(tsParticles);
    })();
  }, []);

  return (
    <ParticlesContext.Provider value={{ engine }}>
      {children}
    </ParticlesContext.Provider>
  );
};

// Usage: wrap app
<ParticlesProvider particlesInit={loadFull}>
  <App />
</ParticlesProvider>
```

### Angular — **Injectable Service with DI**

```ts
@Injectable({ providedIn: "root" })
export class ParticlesEngineService {
  private engine: Engine;
  private initPromise: Promise<void>;

  async init(particlesInit: (e: Engine) => void) {
    if (this.initPromise) return this.initPromise;
    this.initPromise = (async () => {
      const { tsParticles } = await import("@tsparticles/engine");
      await particlesInit(tsParticles);
      this.engine = tsParticles;
    })();
    return this.initPromise;
  }
}

// Usage: app bootstrap
constructor(engine: ParticlesEngineService) {
  engine.init(loadFull);
}
```

### Vue 3 — **Global Plugin + App.use()**

```ts
// ParticlesPlugin.ts
import { App } from "vue";
import type { Engine } from "@tsparticles/engine";

let engineInstance: Engine;

export const ParticlesPlugin = {
  install(app: App, options: { init: (engine: Engine) => Promise<void> }) {
    (async () => {
      const { tsParticles } = await import("@tsparticles/engine");
      await options.init(tsParticles);
      engineInstance = tsParticles;
    })();

    app.provide("tsParticles", () => engineInstance);
  },
};

// Usage: main.ts
app.use(ParticlesPlugin, {
  init: loadFull,
});
```

### Svelte — **Store Singleton**

```ts
// particlesStore.ts
import { writable } from "svelte/store";
import type { Engine } from "@tsparticles/engine";

function createParticlesStore() {
  const { subscribe, set } = writable<Engine | undefined>(undefined);

  const init = async (particlesInit: (e: Engine) => Promise<void>) => {
    const { tsParticles } = await import("@tsparticles/engine");
    await particlesInit(tsParticles);
    set(tsParticles);
  };

  return {
    subscribe,
    init,
  };
}

export const particles = createParticlesStore();

// Usage: App.svelte
<script>
  import { particles } from './particlesStore';
  onMount(() => particles.init(loadFull));
</script>
```

### Solid.js — **Resource or Context**

```tsx
// ParticlesProvider.tsx
import { createContext, useContext, Resource, createResource } from "solid-js";
import type { Engine } from "@tsparticles/engine";

const ParticlesCtx = createContext<Resource<Engine>>();

export const ParticlesProvider = (props) => {
  const [engine] = createResource(async () => {
    const { tsParticles } = await import("@tsparticles/engine");
    await props.particlesInit?.(tsParticles);
    return tsParticles;
  });

  return (
    <ParticlesCtx.Provider value={engine}>
      {props.children}
    </ParticlesCtx.Provider>
  );
};

export const useParticles = () => useContext(ParticlesCtx);
```

### Astro — **Global Layout or Component**

```astro
---
// Particles.astro
import { loadFull } from "@tsparticles/presets";

// Server-side or client-side script
const initParticles = async () => {
  const { tsParticles } = await import("@tsparticles/engine");
  await loadFull(tsParticles);
  // Store in window for client components to use
  window.tsParticlesEngine = tsParticles;
};
---

<script define:vars={{ initParticles }}>
  {
    typeof window !== "undefined" &&
      window.tsParticlesEngine ||
      (await initParticles());
  }
</script>

<slot />
```

## Component Usage (All Frameworks)

Once init is set up:

```tsx
// React
<Particles options={config} id="my-particles" />

// Angular
<ngx-particles [options]="config" id="my-particles"></ngx-particles>

// Vue
<component :options="config" id="my-particles" />

// Svelte
<Particles options={config} id="my-particles" />

// Solid
<Particles options={config} id="my-particles" />
```

**Components are lightweight** — they only:
1. Query the shared engine
2. Load particle config
3. Create container

## Decision Checklist

For each framework wrapper, ask:

- [ ] **What's the idiomatic init container?** (Context, Service, Plugin, Store, etc.)
- [ ] **How to cache a singleton?** (useState, Injectable, global var, writable, ref, etc.)
- [ ] **How to expose it to components?** (Hook, inject(), provide(), store subscription, etc.)
- [ ] **Error handling?** (Try-catch, RxJS subject, promise rejection, etc.)
- [ ] **Type safety?** (Full TS support, inference possible?)
- [ ] **Backward compat?** (Can old pattern still work? Yes for React/Angular, assess per framework)

## Migration Path for Existing Code

**Old pattern** (init per component):
```
Component 1: init engine → load config → render
Component 2: init engine → load config → render  ← redundant init
Component 3: init engine → load config → render  ← redundant init
```

**New pattern** (init once):
```
App: init engine + plugins → cache

Component 1: get engine → load config → render
Component 2: get engine → load config → render
Component 3: get engine → load config → render
```

### Simple Migration Script

For each wrapper repo:
1. Create init container (`ParticlesProvider`, `Service`, `Plugin`, etc.)
2. Add `init()` method / function
3. Expose cached engine to components
4. Update component to **check for cached engine first, fall back to inline init**
5. Update README with new vs. old pattern
6. Provide examples for both

## Performance Metrics to Track

After implementing:

- [ ] Time to first Particles component
- [ ] Memory footprint (components with/without cache)
- [ ] Bundle size delta
- [ ] Initialization time (one-time, then 0ms per instance)

## References

- [React Advanced Patterns](https://react.dev/reference/react/useContext)
- [Angular Dependency Injection](https://angular.io/guide/dependency-injection)
- [Vue 3 Plugins](https://vuejs.org/guide/reusability/plugins.html)
- [Svelte Stores](https://svelte.dev/docs/svelte-store)
- [Solid Resources](https://docs.solidjs.com/reference/reactive-utilities/createResource)
