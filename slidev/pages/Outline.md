---
theme: seriph
background: https://cover.sli.dev
title: Model Context Protocol (MCP)
info: |
  ## Model Context Protocol (MCP)
  Understanding Anthropic's standardized approach to AI tool integration.
class: text-center
drawings:
  persist: false
transition: slide-left
mdc: true
layout: cover
paginate: true
---

<style>
/* 1) Reset & initialize counters on the root */
:root {
  counter-reset: slides var(--slidev-total); /* --slidev-total is injected by Slidev */
}
/* 2) Increment a per-slide counter */
section[data-title] {
  counter-increment: slides;
  position: relative;
}
/* 3) Inject title + pagination before each slide’s content */
section[data-title]::before {
  content: attr(data-title) " — " counter(slides) "/" counter(slides);
  position: absolute;
  top: 1rem;
  left: 1rem;
  font-size: 0.9rem;
  font-weight: 500;
  pointer-events: none;
  opacity: 0.75;
}
/* 4) Push down slide content so it doesn’t overlap the header */
.slide-content {
  padding-top: 2.5rem;
}
</style>

---
src: ./welcome.md
---

---
src: ./history-short.md
---

---
src: ./solution.md
---

---
src: ./existing-solutions.md
---

---
src: ./limitations.md
---

---
src: ./resources.md
---
