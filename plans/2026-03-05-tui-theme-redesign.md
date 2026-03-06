# TUI Theme Redesign Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Restyle the VitePress docs with a terminal/TUI aesthetic — monospace chrome, box-drawing borders, prompt prefixes, blinking cursor, and a Neovim-style statusline footer — while keeping Catppuccin macchiato colors and readable proportional body text.

**Architecture:** CSS-first approach using VitePress CSS variable overrides and scoped custom styles. Two small Vue components (StatusLine, BlinkingCursor) injected via layout slots. No custom Layout.vue — we use the default layout with slot injection to minimize maintenance burden on VitePress upgrades.

**Tech Stack:** VitePress 1.6+, Vue 3, `@fontsource/jetbrains-mono`, Catppuccin macchiato/mauve CSS.

---

### Task 1: Install JetBrains Mono and set up font imports

**Files:**
- Modify: `package.json` (add dependency)
- Create: `docs/.vitepress/theme/tui-theme.css` (new master stylesheet)
- Modify: `docs/.vitepress/theme/index.ts` (import new stylesheet)

**Step 1: Install @fontsource/jetbrains-mono**

Run: `bun add @fontsource/jetbrains-mono`

**Step 2: Create `tui-theme.css` with font setup and CSS variable overrides**

```css
/* === Font imports === */
@import '@fontsource/jetbrains-mono/400.css';
@import '@fontsource/jetbrains-mono/500.css';
@import '@fontsource/jetbrains-mono/600.css';
@import '@fontsource/jetbrains-mono/700.css';

/* === Font stacks === */
:root {
  --tui-font-mono: 'JetBrains Mono', 'Cascadia Code', 'Fira Code', monospace;
  --tui-font-body: 'Manrope', system-ui, sans-serif;
}

/* === VitePress variable overrides === */
:root {
  --vp-font-family-base: var(--tui-font-body);
  --vp-font-family-mono: var(--tui-font-mono);
}
```

**Step 3: Import `tui-theme.css` in theme `index.ts`**

Add after the catppuccin import:

```ts
import './tui-theme.css';
```

**Step 4: Remove Google Fonts import from `docs/index.md`**

Remove the `@import url('https://fonts.googleapis.com/css2?family=Manrope:...')` line and the Space Grotesk references. Install Manrope via fontsource instead:

Run: `bun add @fontsource/manrope`

Add to `tui-theme.css`:

```css
@import '@fontsource/manrope/400.css';
@import '@fontsource/manrope/500.css';
@import '@fontsource/manrope/600.css';
@import '@fontsource/manrope/700.css';
@import '@fontsource/manrope/800.css';
```

**Step 5: Verify the docs build and fonts load**

Run: `bun run docs:build`
Expected: Build succeeds with no errors.

**Step 6: Commit**

```bash
git add package.json bun.lockb docs/.vitepress/theme/tui-theme.css docs/.vitepress/theme/index.ts docs/index.md
git commit -m "feat(docs): add JetBrains Mono via fontsource and create tui-theme.css

- Install @fontsource/jetbrains-mono and @fontsource/manrope
- Create tui-theme.css with font stacks and VP variable overrides
- Remove Google Fonts external import from landing page
"
```

---

### Task 2: Monospace chrome — nav, sidebar, headings

**Files:**
- Modify: `docs/.vitepress/theme/tui-theme.css`

**Step 1: Add nav bar monospace styling**

Append to `tui-theme.css`:

```css
/* === Nav bar === */
.VPNav .VPNavBarTitle .title {
  font-family: var(--tui-font-mono);
  font-weight: 600;
  font-size: 14px;
  letter-spacing: -0.02em;
}

.VPNav .VPNavBarMenuLink {
  font-family: var(--tui-font-mono);
  font-size: 13px;
  font-weight: 500;
}

/* --flag style nav links */
.VPNav .VPNavBarMenuLink::before {
  content: '--';
  opacity: 0.4;
}

/* Box-drawing bottom border for nav */
.VPNav .VPNavBar .divider {
  border-bottom: none;
}

.VPNav .VPNavBar {
  border-bottom: 1px solid var(--vp-c-divider);
  border-image: repeating-linear-gradient(
    to right,
    var(--vp-c-divider) 0,
    var(--vp-c-divider) 1ch,
    transparent 1ch,
    transparent 1.1ch
  ) 1;
}
```

**Step 2: Add sidebar monospace styling**

```css
/* === Sidebar === */
.VPSidebar .VPSidebarItem .text {
  font-family: var(--tui-font-mono);
  font-size: 13px;
}

/* Prompt prefix for active item */
.VPSidebar .VPSidebarItem.is-active > .item > .indicator + div .text::before {
  content: '❯ ';
  color: var(--vp-c-brand-1);
}

/* Dot prefix for inactive items */
.VPSidebar .VPSidebarItem.is-link:not(.is-active) > .item > div .text::before {
  content: '· ';
  opacity: 0.4;
}

/* Section headers styled as comments */
.VPSidebar .VPSidebarItem.level-0 > .item .text {
  font-weight: 700;
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.06em;
}

.VPSidebar .VPSidebarItem.level-0 > .item .text::before {
  content: '# ';
  color: var(--vp-c-text-3);
}

/* Tree-style connectors */
.VPSidebar .VPSidebarItem.level-0 .items {
  border-left: 1px solid var(--vp-c-divider);
  margin-left: 6px;
  padding-left: 12px;
}
```

**Step 3: Add heading styles**

```css
/* === Headings === */
.vp-doc h1,
.vp-doc h2,
.vp-doc h3,
.vp-doc h4 {
  font-family: var(--tui-font-mono);
}

/* h1 prompt prefix */
.vp-doc h1 {
  letter-spacing: -0.02em;
}

.vp-doc h1::before {
  content: '❯ ';
  color: var(--vp-c-brand-1);
  font-weight: 400;
}

/* h2 box-drawing underline */
.vp-doc h2 {
  border-bottom: none;
  padding-bottom: 4px;
}

.vp-doc h2::after {
  content: '';
  display: block;
  margin-top: 6px;
  height: 1px;
  background: repeating-linear-gradient(
    to right,
    var(--vp-c-divider) 0,
    var(--vp-c-divider) 1ch,
    transparent 1ch,
    transparent 1.5ch
  );
}
```

**Step 4: Verify dev server renders correctly**

Run: `bun run docs:dev` and check nav, sidebar, headings visually.

**Step 5: Commit**

```bash
git add docs/.vitepress/theme/tui-theme.css
git commit -m "feat(docs): monospace chrome for nav, sidebar, and headings

- Nav links styled as --flags with mono font
- Sidebar items with prompt/dot prefixes and tree connectors
- Section headers styled as # comments
- Headings with prompt prefix and dashed underlines
"
```

---

### Task 3: Box-drawing custom containers (tip/warning/danger)

**Files:**
- Modify: `docs/.vitepress/theme/tui-theme.css`

**Step 1: Override default custom block styling**

Append to `tui-theme.css`:

```css
/* === Custom containers with box-drawing borders === */
.vp-doc .custom-block {
  border: 1px solid var(--vp-c-divider);
  border-radius: 0;
  padding: 16px 20px;
  margin: 16px 0;
  position: relative;
  background: var(--vp-c-bg-soft);
}

.vp-doc .custom-block::before {
  font-family: var(--tui-font-mono);
  font-size: 12px;
  font-weight: 600;
  letter-spacing: 0.04em;
  text-transform: uppercase;
  position: absolute;
  top: -1px;
  left: 12px;
  transform: translateY(-50%);
  padding: 0 6px;
  background: var(--vp-c-bg);
}

/* Remove default left border */
.vp-doc .custom-block.tip,
.vp-doc .custom-block.info,
.vp-doc .custom-block.warning,
.vp-doc .custom-block.danger,
.vp-doc .custom-block.details {
  border-left-width: 1px;
}

/* Box-drawing corners via pseudo-elements */
.vp-doc .custom-block.tip { border-color: var(--vp-c-brand-1); }
.vp-doc .custom-block.tip::before { content: '── tip'; color: var(--vp-c-brand-1); }

.vp-doc .custom-block.info { border-color: var(--vp-c-brand-2); }
.vp-doc .custom-block.info::before { content: '── info'; color: var(--vp-c-brand-2); }

.vp-doc .custom-block.warning { border-color: var(--vp-c-warning-1); }
.vp-doc .custom-block.warning::before { content: '── warning'; color: var(--vp-c-warning-1); }

.vp-doc .custom-block.danger { border-color: var(--vp-c-danger-1); }
.vp-doc .custom-block.danger::before { content: '── danger'; color: var(--vp-c-danger-1); }

.vp-doc .custom-block.details { border-color: var(--vp-c-divider); }
.vp-doc .custom-block.details::before { content: '── details'; color: var(--vp-c-text-2); }

/* Hide default title since we use ::before */
.vp-doc .custom-block .custom-block-title {
  font-family: var(--tui-font-mono);
  font-size: 13px;
  font-weight: 600;
}
```

**Step 2: Verify with a docs page that has custom blocks**

Run: `bun run docs:dev` and navigate to a page with `::: tip` blocks.

**Step 3: Commit**

```bash
git add docs/.vitepress/theme/tui-theme.css
git commit -m "feat(docs): box-drawing styled custom containers

- Replace rounded containers with sharp box-drawing borders
- Floating labels (tip/info/warning/danger) positioned on border
- Monospace container titles
"
```

---

### Task 4: Blinking cursor component

**Files:**
- Create: `docs/.vitepress/theme/components/BlinkingCursor.vue`
- Modify: `docs/.vitepress/theme/tui-theme.css`

**Step 1: Create BlinkingCursor component**

```vue
<template>
  <span class="tui-cursor" aria-hidden="true">█</span>
</template>
```

**Step 2: Add cursor CSS to tui-theme.css**

```css
/* === Blinking cursor === */
.tui-cursor {
  display: inline-block;
  color: var(--vp-c-brand-1);
  animation: tui-blink 1s step-end infinite;
  font-family: var(--tui-font-mono);
  font-size: 0.9em;
  vertical-align: baseline;
  margin-left: 2px;
  line-height: 1;
}

@keyframes tui-blink {
  0%, 100% { opacity: 1; }
  50% { opacity: 0; }
}
```

**Step 3: Add cursor to active nav item via CSS (no component needed for nav)**

```css
/* Blinking cursor on active nav link */
.VPNav .VPNavBarMenuLink.active::after {
  content: '█';
  color: var(--vp-c-brand-1);
  animation: tui-blink 1s step-end infinite;
  margin-left: 4px;
  font-size: 0.85em;
}
```

**Step 4: Commit**

```bash
git add docs/.vitepress/theme/components/BlinkingCursor.vue docs/.vitepress/theme/tui-theme.css
git commit -m "feat(docs): add blinking cursor component and active nav indicator

- BlinkingCursor.vue for reuse in hero/landing
- CSS-only cursor on active nav link
"
```

---

### Task 5: Neovim-style statusline footer

**Files:**
- Create: `docs/.vitepress/theme/components/StatusLine.vue`
- Modify: `docs/.vitepress/theme/index.ts` (register component in layout slot)
- Modify: `docs/.vitepress/theme/tui-theme.css`

**Step 1: Create StatusLine component**

```vue
<script setup>
import { useRoute, useData } from 'vitepress';
import { computed } from 'vue';

const route = useRoute();
const { page, frontmatter } = useData();

const mode = computed(() => {
  const layout = frontmatter.value.layout;
  if (layout === 'home') return 'HOME';
  return 'NORMAL';
});

const filePath = computed(() => {
  const path = route.path;
  return path === '/' ? 'index.md' : `${path.replace(/^\//, '')}.md`;
});

const section = computed(() => {
  const path = route.path;
  if (path === '/') return 'root';
  const parts = path.split('/').filter(Boolean);
  return parts[0] || 'root';
});

const lastUpdated = computed(() => {
  if (!page.value.lastUpdated) return '';
  const date = new Date(page.value.lastUpdated);
  return date.toISOString().slice(0, 10);
});
</script>

<template>
  <footer class="tui-statusline" aria-label="Page info">
    <div class="tui-statusline__left">
      <span class="tui-statusline__mode" :data-mode="mode">{{ mode }}</span>
      <span class="tui-statusline__sep"></span>
      <span class="tui-statusline__file">{{ filePath }}</span>
    </div>
    <div class="tui-statusline__right">
      <span class="tui-statusline__section">{{ section }}</span>
      <span class="tui-statusline__sep"></span>
      <span v-if="lastUpdated" class="tui-statusline__date">{{ lastUpdated }}</span>
      <span v-if="lastUpdated" class="tui-statusline__sep"></span>
      <span class="tui-statusline__branch">GPL-3.0</span>
    </div>
  </footer>
</template>
```

**Step 2: Add statusline CSS to tui-theme.css**

```css
/* === Neovim-style statusline === */
.tui-statusline {
  position: fixed;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 50;
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 28px;
  padding: 0 12px;
  font-family: var(--tui-font-mono);
  font-size: 12px;
  font-weight: 500;
  background: var(--vp-c-bg-soft);
  border-top: 1px solid var(--vp-c-divider);
  color: var(--vp-c-text-2);
  user-select: none;
}

.tui-statusline__left,
.tui-statusline__right {
  display: flex;
  align-items: center;
  gap: 0;
}

.tui-statusline__mode {
  padding: 0 10px;
  font-weight: 700;
  font-size: 11px;
  letter-spacing: 0.04em;
  color: var(--vp-c-bg);
  background: var(--vp-c-brand-1);
  line-height: 28px;
  margin-left: -12px;
}

.tui-statusline__mode[data-mode="HOME"] {
  background: var(--vp-c-brand-2);
}

.tui-statusline__sep {
  display: inline-block;
  margin: 0 2px;
  color: var(--vp-c-divider);
}

.tui-statusline__sep::after {
  content: '│';
}

.tui-statusline__file {
  padding: 0 8px;
  color: var(--vp-c-text-1);
}

.tui-statusline__section {
  padding: 0 8px;
  color: var(--vp-c-text-2);
}

.tui-statusline__date {
  padding: 0 8px;
  color: var(--vp-c-text-3);
}

.tui-statusline__branch {
  padding: 0 10px;
  font-weight: 600;
  color: var(--vp-c-bg);
  background: var(--vp-c-brand-3, var(--vp-c-brand-1));
  line-height: 28px;
  margin-right: -12px;
}

/* Push page content up so statusline doesn't overlap */
body {
  padding-bottom: 28px;
}

/* Hide on mobile for space */
@media (max-width: 640px) {
  .tui-statusline__section,
  .tui-statusline__date,
  .tui-statusline__sep:nth-child(4),
  .tui-statusline__sep:nth-child(6) {
    display: none;
  }
}
```

**Step 3: Register StatusLine in theme index.ts**

Update `docs/.vitepress/theme/index.ts` to use a custom Layout wrapper that injects StatusLine into the `layout-bottom` slot:

```ts
import StatusLine from './components/StatusLine.vue';
```

And update the export to use `enhanceApp` or a layout wrapper with the `layout-bottom` slot. The cleanest approach in VitePress is wrapping DefaultTheme.Layout:

Create `docs/.vitepress/theme/TuiLayout.vue`:

```vue
<script setup>
import DefaultTheme from 'vitepress/theme';
import StatusLine from './components/StatusLine.vue';

const { Layout } = DefaultTheme;
</script>

<template>
  <Layout>
    <template #layout-bottom>
      <StatusLine />
    </template>
  </Layout>
</template>
```

Then in `index.ts`, replace `...DefaultTheme` spread with explicit Layout:

```ts
import TuiLayout from './TuiLayout.vue';

export default {
  Layout: TuiLayout,
  // ... keep enhanceApp/setup
};
```

**Step 4: Verify statusline renders**

Run: `bun run docs:dev` — statusline should be visible at bottom of viewport on all pages.

**Step 5: Commit**

```bash
git add docs/.vitepress/theme/components/StatusLine.vue docs/.vitepress/theme/TuiLayout.vue docs/.vitepress/theme/tui-theme.css docs/.vitepress/theme/index.ts
git commit -m "feat(docs): add Neovim-style statusline footer

- Fixed statusline with mode, filepath, section, date, license
- Powerline-style segments with Catppuccin accent colors
- Responsive: hides secondary info on mobile
- TuiLayout wrapper for slot injection
"
```

---

### Task 6: Landing page restyle

**Files:**
- Modify: `docs/index.md`
- Modify: `docs/.vitepress/theme/tui-theme.css`
- Modify: `docs/.vitepress/theme/TuiLayout.vue` (add cursor to hero)

**Step 1: Restyle hero section via CSS**

Append to `tui-theme.css`:

```css
/* === Hero TUI styling === */
.VPHero .name {
  font-family: var(--tui-font-mono) !important;
}

.VPHero .text {
  font-family: var(--tui-font-mono) !important;
  font-size: 2rem !important;
}

.VPHero .tagline {
  font-family: var(--tui-font-body);
}

/* Action buttons as key prompts */
.VPHero .actions .action .VPButton {
  font-family: var(--tui-font-mono);
  font-size: 13px;
  border-radius: 0;
  text-transform: lowercase;
}

.VPHero .actions .action .VPButton.brand::before {
  content: '[';
  opacity: 0.5;
  margin-right: 2px;
}

.VPHero .actions .action .VPButton.brand::after {
  content: ']';
  opacity: 0.5;
  margin-left: 2px;
}

.VPHero .actions .action .VPButton.alt::before {
  content: '[';
  opacity: 0.5;
  margin-right: 2px;
}

.VPHero .actions .action .VPButton.alt::after {
  content: ']';
  opacity: 0.5;
  margin-left: 2px;
}
```

**Step 2: Add blinking cursor to hero tagline**

In `TuiLayout.vue`, add the cursor via the `home-hero-info-after` slot:

```vue
<template #home-hero-info-after>
  <BlinkingCursor />
</template>
```

And import it:

```ts
import BlinkingCursor from './components/BlinkingCursor.vue';
```

**Step 3: Restyle workflow steps in `index.md`**

Update the `<style>` block in `docs/index.md` — remove the Google Fonts import and Space Grotesk references, update to use CSS variables:

```css
.landing-shell,
.landing-shell .step-title,
.landing-shell h1,
.landing-shell h2 {
  font-family: var(--tui-font-mono);
}

.step-title,
.step-number {
  font-family: var(--tui-font-mono);
  letter-spacing: -0.01em;
}
```

Restyle workflow steps as pipeline output:

```css
.workflow-steps {
  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 1px;
  background: var(--vp-c-divider);
  border-radius: 0;
  overflow: hidden;
  border: 1px solid var(--vp-c-divider);
}

.workflow-step {
  padding: 1.1rem 1.25rem;
  background: var(--vp-c-bg-soft);
  animation: card-enter 330ms ease-out;
}

.workflow-step .step-number {
  display: inline-block;
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.05em;
  color: var(--vp-c-brand-1);
  margin-bottom: 0.5rem;
  font-variant-numeric: tabular-nums;
}

.workflow-step .step-number::before {
  content: '$ ';
  color: var(--vp-c-text-3);
}
```

**Step 4: Update features section styling**

```css
/* === Features grid TUI style === */
.VPFeatures .VPFeature {
  border-radius: 0 !important;
  border: 1px solid var(--vp-c-divider) !important;
}

.VPFeatures .VPFeature .title {
  font-family: var(--tui-font-mono);
  font-size: 14px;
}

.VPFeatures .VPFeature .details {
  font-family: var(--tui-font-body);
}
```

**Step 5: Verify landing page**

Run: `bun run docs:dev` and check the landing page.

**Step 6: Commit**

```bash
git add docs/.vitepress/theme/tui-theme.css docs/.vitepress/theme/TuiLayout.vue docs/index.md
git commit -m "feat(docs): TUI-styled landing page

- Monospace hero with bracket-wrapped action buttons
- Blinking cursor after tagline
- Pipeline-style workflow steps with $ prefix
- Sharp-cornered feature cards
"
```

---

### Task 7: Misc polish and outline/aside styling

**Files:**
- Modify: `docs/.vitepress/theme/tui-theme.css`

**Step 1: Style the "On this page" outline**

```css
/* === Outline (table of contents) === */
.VPDocAsideOutline .outline-title {
  font-family: var(--tui-font-mono);
  font-size: 12px;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

.VPDocAsideOutline .outline-link {
  font-family: var(--tui-font-mono);
  font-size: 12px;
}

/* Active outline item */
.VPDocAsideOutline .outline-link.active {
  color: var(--vp-c-brand-1);
}

.VPDocAsideOutline .outline-link.active::before {
  content: '❯ ';
}
```

**Step 2: Style breadcrumb / edit link / last updated**

```css
/* === Doc footer meta === */
.VPDocFooter .edit-link-button,
.VPDocFooter .last-updated-text {
  font-family: var(--tui-font-mono);
  font-size: 12px;
}

/* Prev/next links */
.VPDocFooter .pager-link .title {
  font-family: var(--tui-font-mono);
}

.VPDocFooter .pager-link .desc {
  font-family: var(--tui-font-mono);
  font-size: 11px;
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

/* Search dialog */
.VPLocalSearchBox .search-input {
  font-family: var(--tui-font-mono);
}
```

**Step 3: Subtle background texture**

```css
/* === Subtle noise for depth === */
.VPDoc,
.VPHome {
  background-image: url("data:image/svg+xml,%3Csvg viewBox='0 0 200 200' xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.9' numOctaves='4' stitchTiles='stitch'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.015'/%3E%3C/svg%3E");
  background-repeat: repeat;
  background-size: 200px 200px;
}
```

**Step 4: Commit**

```bash
git add docs/.vitepress/theme/tui-theme.css
git commit -m "feat(docs): TUI polish for outline, footer, search, and background

- Monospace outline with active prompt prefix
- Styled prev/next and edit links
- Subtle noise texture background
"
```

---

### Task 8: Visual QA and final adjustments

**Files:**
- Possibly modify: `docs/.vitepress/theme/tui-theme.css`
- Possibly modify: `docs/.vitepress/theme/TuiLayout.vue`

**Step 1: Run full build**

Run: `bun run docs:build`
Expected: Clean build with no errors or warnings.

**Step 2: Preview and check all pages**

Run: `bun run docs:preview`

Check:
- Landing page hero, features, workflow steps, demo video
- At least 2 inner doc pages (configuration, installation)
- A page with custom blocks (tip/warning)
- A page with mermaid diagrams
- Mobile responsive view (resize browser)
- Statusline updates when navigating between pages

**Step 3: Fix any visual issues found**

Likely candidates:
- Spacing conflicts between `::before` content and existing styles
- Statusline overlapping content at page bottom
- Mobile nav menu styling
- Custom block `::before` label positioning edge cases

**Step 4: Final commit**

```bash
git add docs/.vitepress/theme/
git commit -m "fix(docs): visual QA adjustments for TUI theme"
```

---

## Summary of new/modified files

| File | Action |
|------|--------|
| `package.json` | Add `@fontsource/jetbrains-mono`, `@fontsource/manrope` |
| `docs/.vitepress/theme/tui-theme.css` | **Create** — all CSS overrides |
| `docs/.vitepress/theme/components/BlinkingCursor.vue` | **Create** — reusable cursor |
| `docs/.vitepress/theme/components/StatusLine.vue` | **Create** — statusline footer |
| `docs/.vitepress/theme/TuiLayout.vue` | **Create** — layout wrapper for slots |
| `docs/.vitepress/theme/index.ts` | Modify — import new CSS, use TuiLayout |
| `docs/index.md` | Modify — remove Google Fonts, restyle workflow steps |
