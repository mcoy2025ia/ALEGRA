# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repository is

This is **not a software application** — it's a folder of client-facing sales/proposal deliverables prepared for Alegra (a PYME/SMB business platform). It has no build system, package manager, git repo, or test suite. There is exactly one piece of "code": a self-contained HTML presentation.

Contents:
- `index.html` — root hub page linking all proposals/projects (see "The hub page" below). Deployed as its own Vercel project (`alegra`).
- `Growth Autopilot/ALEGRA_PYME-Growth_Autopilot.pdf` — exported PDF version of the Growth Autopilot proposal.
- `Growth Autopilot/growth-autopilot-roadmap.html` — the interactive, single-file HTML source for that proposal (open directly in a browser; no server or build step required). Deployed as its own Vercel project (`alegra-growth-autopilot`), with `vercel.json` rewriting `/` to this file.
- `Lifecycle Autopilot/ALEGRA_PYME-Lifecycle_Autopilot.pdf` — PDF proposal for a separate "Lifecycle Autopilot" concept.
- `Lifecycle Autopilot/lifecycle-autopilot-roadmap.html` — interactive HTML source, same pattern as Growth Autopilot. Deployed as its own Vercel project (`alegra-lifecycle-autopilot`), with `vercel.json` rewriting `/` to this file.
- `Cotizador IA Vehiculos Pesados/Sistema de Inteligencia Contractual y Evidencia para Seguros de Vehiculos Pesados.pdf` — PDF for a **real, already-delivered client project** (not an Alegra candidacy proposal): an AI system built for an insurance intermediary client, covering all-risk policies in the heavy-vehicle line. No HTML source exists for this one — PDF only. Deployed as its own Vercel project (`alegra-cotizador-ia-vehiculos-pesados`), with `vercel.json` rewriting `/` to the PDF.

The two Alegra proposals (Growth Autopilot, Lifecycle Autopilot) pitch an AI-first system for PYMEs that follows a **Detect → Understand → Recommend → Act → Measure → Learn** loop: surfacing business signals (sales drops, inactive customers, overdue receivables, churn risk, unattended leads, repurchase propensity) and turning them into human-approved automated actions. The Cotizador IA project is a distinct, separately-scoped real-world deliverable and does not follow this loop structure.

## Deployment architecture

Each folder (`Growth Autopilot`, `Lifecycle Autopilot`, `Cotizador IA Vehiculos Pesados`) is linked to its **own separate Vercel project** under the same team (`team_xuZsmaSFFJlEiYy46IchOZi3`), deployed independently from that folder as the project root. The repo root (with `index.html`) is a fourth, separate Vercel project (`alegra`) that only hosts the hub page and links out to the other three projects' `.vercel.app` URLs. `.vercel/` directories are gitignored per-folder — never commit them.

## The hub page (`index.html`)

Single self-contained dark-themed page (same visual language/tokens as the roadmap HTML files) with two sections:
- **"Candidatura Alegra"** — a two-card grid (`.cards`) for the Growth Autopilot and Lifecycle Autopilot proposals, each with "Ver página" (links to that project's deployed HTML) and "Ver PDF" buttons with thumbnail previews (`assets/*.jpg`).
- **"Proyecto real de cliente"** — a single-card grid (`.cards.single`) for the Cotizador IA project, styled with the amber accent (`.card-tag.amber`) to visually distinguish a real delivered project from the Alegra proposals. Only a "Ver PDF" button since no interactive HTML exists for it.

When adding a new proposal/project card, follow the existing pattern: a `.card` with `.card-tag`, `<h2>` job title or project name, description `<p>`, and `.card-actions` linking to that project's Vercel URL.

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
