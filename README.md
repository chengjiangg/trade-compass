# Trade Compass

A data-driven toolkit prototype for the Department of Statistics Singapore (DOS), helping agencies identify firms with export potential and assess international market potential.

Built as a single-file HTML prototype (`index.html`) — no build step, no dependencies, no backend.

---

## Overview

Trade Compass is organised as a three-view single-page application:

1. **Landing page** — entry point with two tool cards
2. **Identify Firms with Export Potential** — firm-level analysis by UEN or bulk filter
3. **Trade Explorer** — market-level recommendations for a given firm profile

Navigation is handled client-side via a `showView()` function that swaps visible sections, updates the hero text and breadcrumb, and scrolls to top.

---

## Tool 1 — Identify Firms with Export Potential

Helps agencies surface domestically-oriented firms most likely to succeed abroad. Two modes, toggled via a segmented control at the top of the page.

### Mode A — Look up by UEN

Enter a Singapore UEN and retrieve a single firm's export-potential profile.

**Inputs:**

- UEN (validated against SG UEN format patterns: business, local company, T-series)

**Outputs:**

- Firm name and UEN
- SSIC code and description
- Revenue band (5 tiers from "Below $1M" to "Above $500M")
- Employment size band (5 tiers)
- Firm age (in years, with incorporation year)
- **Export potential score** — a 0–100% probability, shown as a large coloured gauge with a fill bar

**Demo UENs provided:** `201912345A`, `53287461K`, `201706982M`, `199204567D`

**Key behaviours:**

- Same UEN always returns the same firm (deterministic hash-seeded generation) — demos are reproducible
- "Explore markets for this firm" button sends the firm's profile into the Trade Explorer pre-filled
- The UEN input and result card clear automatically when the user navigates away from the page

### Mode B — Filter & list firms

Screen across a corpus of ~2,500 synthetic firms to find candidates matching specific criteria.

**Filter fields** (mirroring the Trade Explorer's Business Characteristics, minus activity description):

- SSIC2025 (2-Digit) — full list, with "Any SSIC" default
- Revenue Band — 5 tiers with "Any" default
- Employment Size — 5 tiers with "Any" default
- Firm Age — 5 bands with "Any age" default
- **Export Potential Range** — dual-handle slider (0–100%), default 40–100%

**Outputs:**

- Sortable table (click any column header): Firm Name, UEN, SSIC, Revenue, Employees, Age, Export Potential
- Summary stats above the table: firm count, average potential, count and % of high-potential firms
- **CSV export** of the full filtered result set (top 200 shown in table; full list exported)

---

## Tool 2 — Trade Explorer

For a given firm profile, recommends overseas markets with bilateral trade data, FTA coverage, and market-level insights.

**Inputs (Business Characteristics):**

- SSIC2025 (2-Digit)
- Revenue Band
- Employment Size
- Firm Age
- Description of Primary Activity (free text)

**Outputs (per market):**

- Country, region, rank, flag
- **Probability-of-success score** (0–100%) shown as a coloured pill
- Bilateral trade value (SG–market), 2024 estimate
- YoY growth, 2024 vs 2023
- Singapore's export share to the market within this SSIC sector
- Market size (GDP, population)
- FTA coverage (AFTA, RCEP, CPTPP, USSFTA, etc.) with status and detail

**Probability filter bar** — appears above the results after "Get Recommendations" is clicked. A dual-handle range slider lets the user narrow destinations to a specific probability range; the results list re-renders in real time, and a count ("Showing 2 of 3 destinations") updates.

---

## Design System

Styled consistently with Department of Statistics Singapore / SingStat visual identity:

- Colours: DOS blue (`#003B71`), DOS red (`#CF152D`), plus success/warning/info tokens
- Typography: Source Sans 3 (via Google Fonts), with JetBrains Mono for UENs
- Shared components: form panels, result cards, dual-handle range sliders, mode toggle, pills, tags
- SG government masthead and standard breadcrumb pattern at the top

---

## Technical Notes

- **Single HTML file** — all CSS and JS inline, no external build or runtime
- **Dependencies:** only Google Fonts (Source Sans 3) via link tag
- **Data:** all firm and market data is synthetic. Firms are generated deterministically from UEN hash seeds; market cards are hand-crafted per sector in `makeMarkets()`
- **No backend** — everything runs client-side
- **Browser support:** modern Chrome / Safari / Firefox / Edge. Uses standard CSS grid, flexbox, and vanilla JS (no frameworks)

### Key functions

| Function                | Purpose                                                                    |
| ----------------------- | -------------------------------------------------------------------------- |
| `showView(view)`        | Switch between the three views; resets UEN state when leaving firm-id page |
| `lookupUEN()`           | Validate UEN and render single-firm result                                 |
| `buildFirmFromUEN(uen)` | Deterministic firm generator (seeded RNG)                                  |
| `applyFirmFilter()`     | Filter the firm corpus by selected criteria                                |
| `renderFirmList()`      | Render sortable firm table with summary stats                              |
| `exportFirmsCSV()`      | Download filtered firms as CSV                                             |
| `handleSubmit()`        | Trade Explorer: generate and render market recommendations                 |
| `renderResults()`       | Render explorer results, applying the probability filter                   |
| `wireDualSlider(...)`   | Attach event listeners and sync visuals for a min/max range slider         |

---

## Disclaimers

- This tool does not constitute trade advice
- All firm data in the prototype is synthetic; no real UEN lookup is performed
- Probability scores are illustrative — produced by a rule-based model for demo purposes
