---
title: Vue Router 5 Upgrade and File-Based Routing
impact: MEDIUM
impactDescription: Vue Router 5 is a drop-in upgrade from v4 unless you use unplugin-vue-router, which must be removed and its imports repointed at the core package; new projects should use its built-in file-based routing and return-based guards instead of the legacy unplugin-vue-router and next() patterns
type: migration
tags: [vue3, vue-router, vue-router-5, file-based-routing, upgrade]
---

# Vue Router 5 Upgrade and File-Based Routing

**Impact: MEDIUM** - Vue Router 5 (released January 2026) is a transition release: if you are not using `unplugin-vue-router`, upgrading from v4 requires **no code changes**. It merges file-based routing (formerly `unplugin-vue-router`) into the core package, so projects that *do* use that plugin must remove it and repoint its imports at `vue-router`. New code should use return-based guards — Router 5 emits a deprecation warning for the `next()` callback — and may adopt typed file-based routing via `definePage()`.

## Task Checklist

- [ ] Upgrade `vue-router` from v4 to v5 — no code modifications required if you don't use `unplugin-vue-router`
- [ ] If you use `unplugin-vue-router`: remove the dependency, repoint every import at the core package, and drop `unplugin-vue-router/client` from `tsconfig.json`
- [ ] Refactor remaining `next()` guards to return-based syntax (now warns in v5)
- [ ] Use `definePage()` for per-route type information in file-based routes
- [ ] Treat data loaders as experimental — prefer guards + lifecycle fetching until stable

## The Problem

```javascript
// WRONG: Installing the standalone plugin for file-based routing (Router 4 era)
import VueRouter from 'vue-router'
import { setupLayouts } from 'virtual:generated-layouts'
import routes from '~pages'

// vue-router 4 + separate unplugin-vue-router dependency
// Two packages to keep in sync, types drift between them
```

```typescript
// WRONG: next()-style guards now emit deprecation warnings in Router 5
router.beforeEach((to, from, next) => {
  if (!isAuthenticated) {
    next('/login')
    return
  }
  next()
})
```

## Solution

```javascript
// CORRECT: File-based routing comes from vue-router itself now
// vite.config.ts — no separate unplugin-vue-router install
import VueRouter from 'vue-router/vite'

export default defineConfig({
  plugins: [
    VueRouter({
      routesFolder: 'src/pages',
      extensions: ['.vue'],
    }),
    vue(),
  ],
})
```

```typescript
// CORRECT: Return-based guards — the modern pattern, no deprecation warning
router.beforeEach((to) => {
  if (!isAuthenticated) {
    return { name: 'Login', query: { redirect: to.fullPath } }
  }
  // Return nothing to allow navigation
})
```

```vue
<!-- CORRECT: definePage() attaches typed route meta in file-based routes -->
<!-- src/pages/users.[id].vue -->
<script setup lang="ts">
definePage({
  meta: {
    requiresAuth: true,
  },
})
</script>
```

## Upgrade Notes

1. **v4 → v5 is drop-in for plain Router 4 projects.** Their only packaging change: the IIFE build no longer bundles `@vue/devtools-api` (v8).
2. **`unplugin-vue-router` users must migrate.** Remove the dependency, then repoint its imports at the core package:
   - `unplugin-vue-router/vite` → `vue-router/vite`
   - `unplugin-vue-router/data-loaders/*` → `vue-router/experimental`
   - `unplugin-vue-router` → `vue-router/unplugin`
   - `unplugin-vue-router/volar/*` → `vue-router/volar/*`
   - drop `unplugin-vue-router/client` from `tsconfig.json`'s type references

   Route generation, `definePage()`, and typed `RouteNamedMap` now ship from the core package.
3. **`next()` deprecation**: v5.0.3 added a deprecation warning for the `next()` callback. See [router-navigation-guard-next-deprecated](router-navigation-guard-next-deprecated.md).
4. **Data loaders** (`defineLoader`, `defineBasicLoader`) are experimental — the API may change between releases. Use them for feedback, not as a load-bearing pattern.
5. **`reroute()`** replaces the deprecated `NavigationResult` for programmatic redirect decisions inside guards.

## Key Points

1. **Upgrade freely without `unplugin-vue-router`** — v5 breaks nothing from plain v4; treat it as v4 plus file-based routing in core
2. **One package** — file-based routing no longer needs `unplugin-vue-router`, but existing users must remove it and repoint imports (see Upgrade Notes)
3. **Return-based guards** — `next()` warns in v5; migrate during the upgrade
4. **Keep data loaders experimental** — don't standardize on them yet

## Reference
- [Migrating to Vue Router 5](https://router.vuejs.org/guide/migration/v4-to-v5)
- [Vue Router Releases](https://github.com/vuejs/router/releases)
