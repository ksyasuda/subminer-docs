# Plausible Tracking Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Keep docs Plausible tracking active for `docs.subminer.moe` while reporting into the existing `subminer.moe` Plausible site through `worker.subminer.moe`.

**Architecture:** The docs theme initializes `@plausible-analytics/tracker` on the client with the root-site domain and proxied event endpoint. The Cloudflare worker proxies event requests to the upstream Plausible instance and keeps script passthrough support for other consumers.

**Tech Stack:** VitePress, Bun tests, Cloudflare Worker, Node builtin test runner

---

### Task 1: Lock Docs Tracker Contract

**Files:**
- Modify: `.vitepress/theme/index.ts`
- Modify: `plausible.test.ts`

**Step 1: Write the failing test**

Add assertions for the approved tracker constants and docs-vs-root-domain split.

**Step 2: Run test to verify it fails**

Run: `bun test plausible.test.ts`
Expected: FAIL until theme uses the explicit constants/asserted strings.

**Step 3: Write minimal implementation**

Extract tracker domain and endpoint constants in the theme and use them in `init`.

**Step 4: Run test to verify it passes**

Run: `bun test plausible.test.ts`
Expected: PASS

### Task 2: Lock Worker Proxy Contract

**Files:**
- Create: `worker.test.js`
- Create: `package.json`
- Modify: `worker.js`

**Step 1: Write the failing test**

Cover:
- `/js/...` script passthrough and cache put
- `/api/event` forwarding to upstream `/api/event`
- `/api/capture` forwarding to upstream `/api/event`
- cookie header stripped before forwarding
- unknown paths return `404`

**Step 2: Run test to verify it fails**

Run: `npm test`
Expected: FAIL because the worker is not yet structured for direct unit testing.

**Step 3: Write minimal implementation**

Refactor `worker.js` to export a testable handler while preserving worker behavior in production.

**Step 4: Run test to verify it passes**

Run: `npm test`
Expected: PASS

### Task 3: Verify End-to-End

**Files:**
- No code changes required

**Step 1: Run docs tests**

Run: `bun test`
Expected: PASS

**Step 2: Run worker tests**

Run: `npm test`
Expected: PASS

**Step 3: Spot-check live docs site**

Confirm `https://docs.subminer.moe` sends a `POST` to `https://worker.subminer.moe/api/event`.
