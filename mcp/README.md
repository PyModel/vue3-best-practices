# vue-skills MCP server

Part of [PyModel/vue3-best-practices](https://github.com/PyModel/vue3-best-practices).

Exposes the Vue 3 best-practice skills in this repo as MCP tools, so any MCP
coding agent can fetch them while working on Vue.

## Tools

- **`vue_best_practices`** — primary tool. No args → full index (every skill's
  workflow + its reference filenames). Pass `skill` to scope to one. Its
  description is written to fire on any Vue signal (`.vue`, `<script setup>`,
  Pinia, Vue Router, Vitest, Vue SSR…).
- **`get_vue_reference`** — full content of one reference file (deep dive).

Progressive disclosure: the index returns overviews + a reference list; the
agent pulls only the references it needs.

## Setup

Published as [`@pymodel/vue-skills-mcp`](https://www.npmjs.com/package/@pymodel/vue-skills-mcp)
with the skills bundled, so nothing needs cloning.

### Claude Code

```bash
claude mcp add vue-skills -- npx -y @pymodel/vue-skills-mcp
```

### Cursor / Windsurf (`~/.cursor/mcp.json` or `mcp_config.json`)

```json
{
  "mcpServers": {
    "vue-skills": { "command": "npx", "args": ["-y", "@pymodel/vue-skills-mcp"] }
  }
}
```

### From a local clone

```bash
cd mcp && npm install
```

Then point your agent at the checkout, replacing `<REPO>` with its absolute path:

```bash
claude mcp add vue-skills -- node <REPO>/mcp/index.mjs
```

`VUE_SKILLS_DIR` overrides where skills are read from. The published package
reads its bundled `./skills`; a clone falls back to `../skills`.

## About "automatic"

MCP has no push trigger — an agent calls a tool when it judges the tool's
description relevant. There is no way to *force* a call. This server maximizes
the chance by engineering `vue_best_practices`'s description to match any Vue
signal. To make it reliable, add one line to your project's agent rules
(`AGENTS.md`, `.cursorrules`, Claude Code `CLAUDE.md`):

> Before any Vue work, call the `vue_best_practices` MCP tool and apply it.

That rule is the actual "always fetch on Vue work" guarantee; the MCP server is
what makes the content callable.

## Test

```bash
npm test
```
