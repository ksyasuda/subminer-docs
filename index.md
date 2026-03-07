---
layout: home

title: SubMiner
titleTemplate: Immersion Mining Workflow for MPV

hero:
  name: SubMiner
  text: Immersion Mining for MPV
  tagline: Watch media, mine vocabulary, and craft anki cards without leaving the scene.
  image:
    src: /assets/SubMiner.png
    alt: SubMiner logo
  actions:
    - theme: brand
      text: Install
      link: /installation
    - theme: alt
      text: Explore workflow
      link: /mining-workflow

features:
  - icon:
      src: /assets/mpv.svg
      alt: mpv icon
    title: Built for mpv
    details: Tracks subtitles through mpv IPC in real time, with a single launch path and no external bridge services.
    link: /usage
    linkText: How it works
  - icon:
      src: /assets/yomitan-icon.svg
      alt: Yomitan logo
    title: Yomitan Integration
    details: Keep your flow moving with instant word lookups and context-aware card creation directly from subtitles.
    link: /mining-workflow
    linkText: Mining workflow
  - icon:
      src: /assets/anki-card.svg
      alt: Anki card icon
    title: Anki Card Enrichment
    details: Auto-fills card fields with subtitle sentence, clipping, image, and translation so you can focus on learning.
    link: /anki-integration
    linkText: Anki integration
  - icon:
      src: /assets/highlight.svg
      alt: Highlight icon
    title: Reading Annotations
    details: Combines N+1 targeting, character-name matching, frequency highlighting, and JLPT tagging so useful cues stay visible while you read.
    link: /subtitle-annotations
    linkText: Annotation details
  - icon:
      src: /assets/tokenization.svg
      alt: Tokenization icon
    title: Immersion Tracking
    details: Captures subtitle and mining telemetry to SQLite, with daily/monthly rollups for progress clarity.
    link: /immersion-tracking
    linkText: Tracking details
  - icon:
      src: /assets/subtitle-download.svg
      alt: Subtitle download icon
    title: Subtitle Download & Sync
    details: Pull and synchronize subtitles with Jimaku plus alass/ffsubsync in one cohesive workflow.
    link: /configuration#jimaku
    linkText: Jimaku integration
---

<script setup>
const demoAssetVersion = '20260223-2';
</script>

<div class="landing-shell">
  <section class="workflow-section">
    <h2>How it fits together</h2>
    <div class="workflow-steps">
      <div class="workflow-step" style="animation-delay: 0ms">
        <div class="step-number">01</div>
        <div class="step-title">Start</div>
        <div class="step-desc">Launch with the wrapper or existing mpv setup and keep subtitles in sync.</div>
      </div>
      <div class="workflow-connector" aria-hidden="true"></div>
      <div class="workflow-step" style="animation-delay: 60ms">
        <div class="step-number">02</div>
        <div class="step-title">Lookup</div>
        <div class="step-desc">Hover or click a token in the interactive overlay to open Yomitan context.</div>
      </div>
      <div class="workflow-connector" aria-hidden="true"></div>
      <div class="workflow-step" style="animation-delay: 120ms">
        <div class="step-number">03</div>
        <div class="step-title">Mine</div>
        <div class="step-desc">Create cards from Yomitan or mine sentence cards directly from subtitle lines.</div>
      </div>
      <div class="workflow-connector" aria-hidden="true"></div>
      <div class="workflow-step" style="animation-delay: 180ms">
        <div class="step-number">04</div>
        <div class="step-title">Enrich</div>
        <div class="step-desc">Automatically attach timing-accurate audio, sentence text, and visual evidence.</div>
      </div>
      <div class="workflow-connector" aria-hidden="true"></div>
      <div class="workflow-step" style="animation-delay: 240ms">
        <div class="step-number">05</div>
        <div class="step-title">Track</div>
        <div class="step-desc">Review immersion history and repeat high-value patterns over time.</div>
      </div>
    </div>
  </section>

  <section class="demo-section">
    <h2>See it in action</h2>
    <p>Subtitles, lookup flow, and card enrichment from a real playback session.</p>
    <div class="demo-window">
      <div class="demo-window__bar">
        <span class="demo-window__dot"></span>
        <span class="demo-window__dot"></span>
        <span class="demo-window__dot"></span>
        <span class="demo-window__title">subminer -- playback</span>
      </div>
      <video controls playsinline preload="metadata" :poster="`/assets/minecard-poster.jpg?v=${demoAssetVersion}`">
        <source :src="`/assets/minecard.webm?v=${demoAssetVersion}`" type="video/webm" />
        <source :src="`/assets/minecard.mp4?v=${demoAssetVersion}`" type="video/mp4" />
        <a :href="`/assets/minecard.webm?v=${demoAssetVersion}`" target="_blank" rel="noreferrer">
          <img :src="`/assets/minecard.webp?v=${demoAssetVersion}`" alt="SubMiner demo Animated fallback" style="width: 100%; height: auto;" />
        </a>
      </video>
    </div>
  </section>
</div>

<style>
.landing-shell {
  max-width: 1120px;
  margin: 0 auto;
  padding: 0.5rem 1rem 4rem;
}

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

/* === Workflow === */
.workflow-section {
  margin: 2.4rem auto 0;
  padding: 0;
}

.workflow-section h2,
.demo-section h2 {
  font-size: 1.45rem;
  font-weight: 600;
  letter-spacing: -0.01em;
  margin-bottom: 1rem;
}

.workflow-steps {
  display: flex;
  align-items: stretch;
  gap: 0;
  border: 1px solid var(--vp-c-divider);
  overflow: hidden;
}

.workflow-step {
  flex: 1;
  padding: 1.2rem 1.25rem;
  background: var(--vp-c-bg-soft);
  animation: step-enter 400ms ease-out both;
  position: relative;
  transition: background 180ms ease;
}

.workflow-step:hover {
  background: hsla(232, 23%, 18%, 0.6);
}

.workflow-step:hover .step-number {
  color: var(--vp-c-brand-1);
  text-shadow: 0 0 12px hsla(267, 83%, 80%, 0.3);
}

.workflow-connector {
  width: 1px;
  background: var(--vp-c-divider);
  flex-shrink: 0;
}

.workflow-step .step-number {
  display: inline-block;
  font-size: 0.7rem;
  font-weight: 700;
  letter-spacing: 0.05em;
  color: var(--vp-c-text-3);
  margin-bottom: 0.5rem;
  font-variant-numeric: tabular-nums;
  transition: color 180ms ease, text-shadow 180ms ease;
}

.workflow-step .step-number::before {
  content: '$ ';
  color: var(--vp-c-text-3);
}

.workflow-step .step-title {
  font-weight: 600;
  font-size: 1rem;
  margin-bottom: 0.35rem;
}

.workflow-step .step-desc {
  font-size: 0.85rem;
  color: var(--vp-c-text-2);
  line-height: 1.5;
}

@keyframes step-enter {
  from {
    opacity: 0;
    transform: translateY(10px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@media (max-width: 960px) {
  .workflow-steps {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: var(--vp-c-divider);
  }
  .workflow-step {
    min-width: 0;
  }
  .workflow-connector {
    display: none;
  }
}

@media (max-width: 640px) {
  .workflow-steps {
    grid-template-columns: 1fr;
  }
}

/* === Demo === */
.demo-section {
  max-width: 960px;
  margin: 0 auto;
  padding: 0;
}

.demo-section p {
  color: var(--vp-c-text-2);
  margin: 0 0 1.2rem;
  line-height: 1.6;
}

.demo-window {
  border: 1px solid var(--vp-c-divider);
  overflow: hidden;
  animation: step-enter 400ms ease-out 300ms both;
  box-shadow:
    0 4px 16px rgba(0, 0, 0, 0.18),
    0 20px 48px rgba(0, 0, 0, 0.14);
}

.demo-window__bar {
  display: flex;
  align-items: center;
  gap: 6px;
  padding: 8px 12px;
  background: var(--vp-c-bg-soft);
  border-bottom: 1px solid var(--vp-c-divider);
}

.demo-window__dot {
  width: 10px;
  height: 10px;
  border-radius: 50%;
}

.demo-window__dot:nth-child(1) { background: #ed8796; }
.demo-window__dot:nth-child(2) { background: #eed49f; }
.demo-window__dot:nth-child(3) { background: #a6da95; }

.demo-window__title {
  font-family: var(--tui-font-mono);
  font-size: 11px;
  color: var(--vp-c-text-3);
  margin-left: 6px;
}

.demo-window video {
  width: 100%;
  display: block;
}
</style>
