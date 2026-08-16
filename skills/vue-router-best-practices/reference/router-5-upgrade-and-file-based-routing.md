---
title: Vue Router 5 Upgrade and File-Based Routing
impact: MEDIUM
impactDescription: Vue Router 5 is a drop-in upgrade from v4, but new projects should use its built-in file-based routing and return-based guards instead of the legacy unplugin-vue-router and next() patterns
type: migration
tags: [vue3, vue-router, vue-router-5, file-based-routing, upgrade]
---

# Vue Router 5 Upgrade and File-Based Routing

**Impact: MEDIUM** - Vue Router 5 (released January 2026) is a transition release: upgrading from v4 requires **no code changes**, and it merges file-based routing (formerly `unplugin-vue-router`) into the core package. New code should use return-based guards — Router 5 emits a deprecation warning for the `next()` callback — and may adopt typed file-based routing via `definePage()`.

## Task Checklist

- [ ] Upgrade `vue-router` from v4 to v5 — no code modifications required
- [ ] Replace `unplugin-vue-router` with the router's built-in file-based routing if you used it
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

1. **v4 → v5 is drop-in.** The only packaging change: the IIFE build no longer bundles `@vue/devtools-api` (v8).
2. **File-based routing**: swap `unplugin-vue-router` for the `vue-router/vite` plugin (or the equivalent Nuxt-style config). Route generation, `definePage()`, and typed `RouteNamedMap` now ship from the core package.
3. **`next()` deprecation**: v5.0.3 added a deprecation warning for the `next()` callback. See [router-navigation-guard-next-deprecated](router-navigation-guard-next-deprecated.md).
4. **Data loaders** (`defineLoader`, `defineBasicLoader`) are experimental — the API may change between releases. Use them for feedback, not as a load-bearing pattern.
5. **`reroute()`** replaces the deprecated `NavigationResult` for programmatic redirect decisions inside guards.

## Key Points

1. **Upgrade freely** — v5 breaks nothing from v4; treat it as v4 plus file-based routing in core
2. **One package** — file-based routing no longer needs `unplugin-vue-router`
3. **Return-based guards** — `next()` warns in v5; migrate during the upgrade
4. **Keep data loaders experimental** — don't standardize on them yet

## Reference
- [Migrating to Vue Router 5](https://router.vuejs.org/guide/migration/v4-to-v5)
- [Vue Router Releases](https://github.com/vuejs/router/releases)
