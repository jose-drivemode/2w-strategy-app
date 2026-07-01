# 2W Strategy — Leadership Workshop App

A single-page leadership workshop tool for exploring a strategic decision framework across 8 domains. Built for a shared screen session with executive teams at Drivemode.

**Live:** https://jose-drivemode.github.io/2w-strategy-app/

---

## What it does

The app presents 8 strategic domains — one governance precondition and seven decision domains — in an interactive two-panel layout. For each domain, a leadership team can:

- Review **observed signals** (colour-coded by type, with source and confidence metadata)
- Read **cross-industry analogies** for strategic context
- Explore **Drivemode capabilities analysis** with supporting evidence per capability
- Navigate **strategic trade-offs** using a draggable spectrum lever (position drives typography weight on each side)
- Read the domain **implication** as a synthesis conclusion
- Optionally enable **facilitation mode** to collect group votes and lock a decision (ANCHOR / DEPRIORITIZE / IGNORE)

All decisions and lever positions persist to `localStorage` across sessions.

---

## Stack

- **Single file:** `index.html` — all HTML, CSS, and JS in one file, no build step, no framework, no dependencies
- **Data:** `data.json` — fetched async on load, drives all content
- **Fonts:** EB Garamond (editorial/analytical text) + Inter (data/labels/UI) via Google Fonts
- **Hosting:** GitHub Pages, deployed from `main` branch root

---

## Updating content

Content lives entirely in `data.json`. The app code does not need to change when domains are updated.

**Workflow:**
1. Regenerate `data.json` in the source repository (`2w-strategy/`)
2. Copy it into this repo:
   ```bash
   cp ../2w-strategy/data.json ./data.json
   ```
3. Commit and push:
   ```bash
   git add data.json && git commit -m "Refresh data.json" && git push
   ```

GitHub Pages rebuilds in ~1 minute.

---

## Data schema

`data.json` is an array of domain objects. All 8 domains are currently full.

```jsonc
{
  "id": 1,                          // integer, 0-indexed
  "number": "01",                   // string, zero-padded
  "title": "Governance and Mandate",
  "type": "domain",                 // "domain" | "precondition" (reserved)
  "full": true,                     // false = stub card, no detail view

  "domainConfidence": "Medium",     // "High" | "Medium" | "Low"
  "domainConfidenceNote": "...",    // paragraph explaining confidence rating
  "domainWarning": false,           // true = show ⚠ warning in confidence block

  "signals": [
    {
      "label": "Competitor Benchmark",
      "missionSource": "M2",        // "M1" | "M2" | "M3" | "M4" | "M3/M4"
      "confidence": "High",         // "High" | "Medium" | "Low–Medium" | "Low"
      "claim": "...",
      "sources": "..."
    }
  ],

  "crossIndustryPatterns": [
    { "title": "...", "body": "..." }
  ],
  "crossIndustrySubtitle": "Interpretive Analogy",   // string | null

  "implications": "...",
  "tensionsNote": "...",            // italic note shown before trade-off list | null

  "tensions": [
    {
      "title": "The Investment Tension: Subtitle",
      // title format: "Name: Subtitle" — colon separates rendering
      "signal": "...",
      "counterSignal": "...",
      "theTension": "..."
    }
  ],

  "capabilities": [
    {
      "capability": "Cloud Architecture & Engineering",
      "assessment": "Leverage / Optimize", // "Leverage / Optimize" | "Invest / Scale" | "Explore / Acquire"
      "evidence": ["...", "..."],
      "implication": "..."
    }
  ]
}
```

**Notes:**
- `**bold**` markdown in `claim` and `description` fields renders as `<strong>`
- No em-dashes anywhere in content (house rule enforced)
- `localStorage` key: `drivemode_wkshp_v1` — changing this clears all saved session state
- `domainWarning: true` is used for Domain 5 (Regional Differentiation) — single-source data

---

## Facilitation mode

The voting panel is hidden by default. Enable it via the **"Enable voting"** toggle pinned at the bottom of each domain detail view. When enabled:

- Three vote buttons: ANCHOR / DEPRIORITIZE / IGNORE
- Votes accumulate (multiple taps = multiple votes, no deduplication)
- "Lock In Decision" button appears once any vote is cast — plurality wins
- Locked state is shown in the detail header and on the overview card
- "Change decision" or "Reset votes" available after locking

Facilitation mode state is session-only (resets on page reload). Vote counts and locked decisions persist to `localStorage`.

---

## Local development

No build step needed. Open `index.html` directly — but `data.json` requires a server context to fetch. Use any static server:

```bash
npx serve .
# or
python3 -m http.server 8080
```

Then open `http://localhost:8080`.

---

## Resetting session state

To clear all saved votes and decisions:

```javascript
localStorage.removeItem('drivemode_wkshp_v1')
```

Run in the browser console, then reload the page.
