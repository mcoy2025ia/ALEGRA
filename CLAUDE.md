# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is **not a software application** — it's a folder of client-facing sales/proposal deliverables prepared for Alegra (a PYME/SMB business platform). It has no build system, package manager, git repo, or test suite. There is exactly one piece of "code": a self-contained HTML presentation.

Contents:
- `Growth Autopilot/ALEGRA_PYME-Growth_Autopilot.pdf` — exported PDF version of the Growth Autopilot proposal.
- `Growth Autopilot/growth-autopilot-roadmap.html` — the interactive, single-file HTML source for that proposal (open directly in a browser; no server or build step required).
- `Lifecycle Autopilot/ALEGRA_PYME-Lifecycle_Autopilot.pdf` — PDF proposal for a separate "Lifecycle Autopilot" concept. No HTML source currently exists for this one.

Both proposals pitch an AI-first system for PYMEs that follows a **Detect → Understand → Recommend → Act → Measure → Learn** loop: surfacing business signals (sales drops, inactive customers, overdue receivables, churn risk, unattended leads, repurchase propensity) and turning them into human-approved automated actions.

## Working with `growth-autopilot-roadmap.html`

This is a single self-contained HTML file (no separate CSS/JS files, no dependencies to install). It renders a dark-themed, tabbed, single-page presentation with client-side content only:

- **Structure**: a fixed top HUD bar, a left nav rail of tabs (`.tab-btn[data-tab=...]`), and one `<section class="tab-panel" id="panel-*">` per tab. Tab order/ids: `overview` (00 · Visión & Problema), `architecture` (01), `flow` (02 · Flujo Growth), `data` (03 · Datos & Scoring), `agents` (04 · Agentes & Guardrails), `roadmap` (05 · Roadmap & Equipo), `metrics` (06 · Métricas & Éxito).
- **Adding a new tab**: add a `<button class="tab-btn" data-tab="X">` in the `.rail` nav and a matching `<section class="tab-panel" id="panel-X">` in `.main` — the existing JS wires up click handling and reveal animations automatically by convention, no extra JS needed.
- **Diagrams**: architecture/flow diagrams are authored as Mermaid syntax inside `<div class="mermaid">` blocks and rendered lazily via the Mermaid CDN script (`mermaid@10.9.1`) the first time a panel becomes visible (see the `activateTab()` function near the bottom of the file). Keep new diagrams as Mermaid text, not hand-drawn SVG, unless doing a bespoke hero graphic like the Detect/Understand/... loop on the overview tab.
- **Styling**: all CSS is inline in a `<style>` block using CSS custom properties defined on `:root` (`--bg`, `--teal`, `--font-display`, etc.). Reuse these tokens rather than hardcoding new colors/fonts.
- **Fonts**: Google Fonts (`Space Grotesk`, `Inter`, `JetBrains Mono`) loaded via `<link>` — requires internet access when viewing.
- **Content language**: proposal copy is written in Spanish (LatAm business Spanish) — match this when editing or adding content.
- **No build/lint/test tooling**: verify changes by opening the file directly in a browser (or via a simple local file open — no dev server needed) and clicking through each tab to confirm panels render and Mermaid diagrams draw without console errors.

## Working with the PDFs

The PDFs are exported/rendered deliverables, not source-controlled generated artifacts — treat them as final output, not something to hand-edit. If a PDF needs to change, the underlying source (e.g. the HTML for Growth Autopilot) should be edited and re-exported instead.
