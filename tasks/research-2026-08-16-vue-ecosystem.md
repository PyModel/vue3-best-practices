# Vue Ecosystem Research — 2026-08-16

Primary-source snapshot of current Vue ecosystem versions and official guidance,
gathered to validate the skills in this repo and plan updates. All version
numbers were read directly from the npm registry / unpkg on 2026-08-16.

## Version matrix (npm registry, 2026-08-16)

| Package | Latest | Source |
|---|---|---|
| vue | 3.5.41 (stable) / 3.6.0-beta.17 (beta) | [registry](https://registry.npmjs.org/vue/latest), [unpkg beta](https://unpkg.com/vue@beta/package.json) |
| pinia | 4.0.3 | [registry](https://registry.npmjs.org/pinia/latest) |
| vue-router | 5.2.0 | [registry](https://registry.npmjs.org/vue-router/latest) |
| vitest | 4.1.10 | [registry](https://registry.npmjs.org/vitest/latest) |
| @vue/test-utils | 2.4.11 | [unpkg](https://unpkg.com/@vue/test-utils/package.json) |
| playwright | 1.62.1 | [registry](https://registry.npmjs.org/playwright/latest) |
| vite | 8.2.1 | [registry](https://registry.npmjs.org/vite/latest) |
| @ai-sdk/vue | 4.0.66 | [registry](https://registry.npmjs.org/@ai-sdk/vue/latest) |
| @upstash/context7-mcp | 4.0.0 | [npm](https://www.npmjs.com/package/@upstash/context7-mcp), [GitHub](https://github.com/upstash/context7) |

## Official Vue guidance (vuejs.org)

**Style guide Priority A (Essential) — unchanged and matches this repo's evals:**
multi-word component names, detailed prop definitions, keyed `v-for`, avoid
`v-if` with `v-for`, component-scoped styling. Priorities B–D categories
unchanged. Source: [vuejs.org/style-guide](https://vuejs.org/style-guide/),
[rules-essential](https://vuejs.org/style-guide/rules-essential.md). The style
guide is now also served as Markdown for LLMs at `/style-guide.md`.

**Vue 3.6 / Vapor Mode status:** stable is still 3.5.x; the newest release post
on the official blog remains [Vue 3.5 (Sept 2024)](https://blog.vuejs.org/).
3.6 is at [3.6.0-beta.17](https://unpkg.com/vue@beta/package.json); Vapor is
opt-in and "feature-complete but still unstable" per beta changelogs. The
official docs (as indexed by Context7, `/vuejs/docs`) still describe Vapor as
an exploratory strategy — 3.6/Vapor is not yet documented as stable guidance.

**Vue Router 5** — released Jan 2026, now at 5.2.0. A "transition release":
[upgrading from v4 requires no code changes](https://router.vuejs.org/guide/migration/v4-to-v5).
Key deltas ([releases](https://github.com/vuejs/router/releases)):
- unplugin-vue-router (file-based routing) merged into the core package
- `next()` callback in navigation guards now emits a deprecation warning
  (return-based guards are the path forward)
- `reroute()` added; `NavigationResult` deprecated (v5.0.3)
- experimental data loaders, typed `definePage` improvements (v5.1.0)
- devtools/@vue/devtools-api v8 alignment; Pinia 4 allowed (v5.2.0)

**Pinia 4** — now at 4.0.3. "Only technically breaking changes":
[ESM-only distribution and `@vue/devtools-api` v8 as a peer dependency](https://github.com/vuejs/pinia/releases).
The store API (`defineStore`, state/getters/actions, `storeToRefs`) is
unchanged from v3. Known issue: devtools-api on Node 25
([#3065](https://github.com/vuejs/pinia/issues/3065)). v2→v3 migration
background: [pinia.vuejs.org](https://pinia.vuejs.org/cookbook/migration-v2-v3.html).

**Context7 MCP** — [@upstash/context7-mcp is at 4.0.0](https://www.npmjs.com/package/@upstash/context7-mcp);
hosted endpoint `https://mcp.context7.com/mcp` with Bearer API key
([GitHub](https://github.com/upstash/context7)). Note: its `/vuejs/docs` index
still predates Vue 3.6 beta content.

## Implications for this repo

1. **vue-router-best-practices** targets "Vue Router 4" — Router 5 is current
   and adds file-based routing, return-based guards (next() deprecating), and
   data loaders. Largest content gap.
2. **vue-pinia-best-practices** — Pinia 4 API unchanged; needs version
   references and an ESM/peer-dep install note only.
3. **vue-testing-best-practices** — Vitest 4 is current; eval fixtures and
   examples should be verified/bumped (plus @vue/test-utils 2.4.11,
   Playwright 1.62.1).
4. **vue-best-practices** — 3.5 coverage is current; keep Vapor framed as
   beta/experimental (stable docs haven't shipped it).
5. **vue-ai-apps** — @ai-sdk/vue@4 alignment is current (4.0.66).
6. **README badge "Vue 3.5+"** remains accurate for stable guidance.
