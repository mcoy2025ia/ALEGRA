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
- `Recursos/` — supporting evidence and source material linked from or feeding the hub page, not deployed as its own Vercel project:
  - `Recursos/Scanner.pdf` — scanned handwritten answers to Alegra's selection-process test; linked directly from the hub's "Recursos del proceso de selección" section.
  - `Recursos/Resumenes/*.png` — one summary image per project (Growth Autopilot, Lifecycle Autopilot, Cotizador IA), linked from the hub's "Resúmenes visuales" section for a quick-glance preview before opening the full page/PDF.
  - `Recursos/Guion/*.md` — narration scripts (Spanish, timestamped cues like `[0:00 – 0:20 | ...]`) for the presentation/skills videos referenced in the hub's "Video de presentación" and "Video de habilidades IA" rows (currently marked "Próximamente" — not yet linked to actual video files).
  - `Recursos/Videos/*.mp4` — recorded videos corresponding to those scripts. Gitignored (too large for GitHub's 100MB file limit) — never commit these. The three per-project case videos (`Caso Growth_Autopilot.mp4`, `Lifecycle_Autopilot.mp4`, `Caso Real.mp4`) are uploaded to a shared public Vercel Blob store (`alegra-videos`, connected to the `alegra-growth-autopilot` project) and linked with a "▶ Ver video del caso" button/link — in the HUD bar (`.hud-video`) for `growth-autopilot-roadmap.html` and `lifecycle-autopilot-roadmap.html`, and as a second card action on the Cotizador IA hub card in `index.html` (since that project has no interactive HTML page of its own). Blob URLs follow `https://ucp3rv9lzkub5ujs.public.blob.vercel-storage.com/videos/<name>.mp4`. To upload a new/replacement video: `vercel blob put <file> --pathname videos/<name>.mp4 --access public --multipart true --rw-token <token>` from within `Growth Autopilot/` (where the store is linked); the presentation/skills videos referenced above are not yet uploaded.
- `Images/` — untracked staging folder of per-project images (`Growth.png`, `Lifecycle.png`, `Cotizador_IA.png`); not currently referenced from any HTML. Likely inputs for the `Recursos/Resumenes` images or a future card update — check with the user before assuming it's safe to delete or repurpose.

The two Alegra proposals (Growth Autopilot, Lifecycle Autopilot) pitch an AI-first system for PYMEs that follows a **Detect → Understand → Recommend → Act → Measure → Learn** loop: surfacing business signals (sales drops, inactive customers, overdue receivables, churn risk, unattended leads, repurchase propensity) and turning them into human-approved automated actions. The Cotizador IA project is a distinct, separately-scoped real-world deliverable and does not follow this loop structure.

## Deployment architecture

Each folder (`Growth Autopilot`, `Lifecycle Autopilot`, `Cotizador IA Vehiculos Pesados`) is linked to its **own separate Vercel project** under the same team (`team_xuZsmaSFFJlEiYy46IchOZi3`), deployed independently from that folder as the project root. The repo root (with `index.html`) is a fourth, separate Vercel project (`alegra`) that only hosts the hub page and links out to the other three projects' `.vercel.app` URLs. `.vercel/` directories are gitignored per-folder — never commit them.

## The hub page (`index.html`)

Single self-contained dark-themed page (same visual language/tokens as the roadmap HTML files) with four sections, in order:
- **"Candidatura Alegra"** — a two-card grid (`.cards`) for the Growth Autopilot and Lifecycle Autopilot proposals, each with "Ver página" (links to that project's deployed HTML) and "Ver PDF" buttons with thumbnail previews (`assets/*.jpg`).
- **"Recursos del proceso de selección"** — a single-card grid (`.cards.single`) containing a `.resource-list` of `.resource-item` rows (handwritten test answers, presentation video, AI-skills video). Each row either links out via `.resource-link` (e.g. `Recursos/Scanner.pdf`) or shows a `.resource-pending` badge ("Próximamente") when the underlying file isn't wired up yet.
- **"Proyecto real de cliente"** — a single-card grid (`.cards.single`) for the Cotizador IA project, styled with the amber accent (`.card-tag.amber`) to visually distinguish a real delivered project from the Alegra proposals. Only a "Ver PDF" button since no interactive HTML exists for it.
- **"Resúmenes visuales"** — a three-card grid (`.cards.triple`) of `.summary-card` links, each a clickable thumbnail (`Recursos/Resumenes/*.png`) opening the full-size summary image in a new tab, one per project.

When adding a new proposal/project card, follow the existing pattern: a `.card` with `.card-tag`, `<h2>` job title or project name, description `<p>`, and `.card-actions` linking to that project's Vercel URL. When adding a resource row instead, follow the `.resource-item` pattern (name + description + link-or-pending badge).

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
