# UP District Mapper — Claude Skill

A Claude skill that generates interactive maps, Excel workbooks, and distance/travel-time sheets for all 75 districts of Uttar Pradesh, India.

---

## What this skill does

| Output | Description |
|--------|-------------|
| **Interactive HTML map** | Self-contained D3 choropleth of UP's 75 districts — click any district for area, density, blocks, and municipal coverage. No server needed. |
| **Full UP Excel workbook** | 4-sheet workbook: district summary (area, pop, density, blocks, urban %), municipal bodies, division rollup, and ~825 block HQs with lat/lon and Maps links |
| **Distance & travel-time sheet** | Given one origin coordinate and a list of block HQs, computes straight-line distance, estimated road distance (×1.3 sinuosity), and travel time (@40 km/h) for each block — colour-coded and sorted nearest-first |

---

## How to use it (as a Claude skill)

Install `SKILL.md` into your Claude skill library. Claude will automatically use it when you:

- Ask for UP district or block data
- Paste a lat/lon + a list of block HQs and say "calculate distances"
- Ask for a downloadable UP map or Excel sheet
- Say "distance from [location] to these blocks"

---

## Required inputs

### For distance/travel-time sheet

| Input | How to provide |
|-------|---------------|
| **Origin** | Paste coordinates as `LAT,LON` (e.g. `25.423038,81.938293`) OR a Google Maps short link (`maps.app.goo.gl/…`) |
| **Block list** | Paste the block table from the UP workbook, or any tab-separated list with columns: Block Name · District · Division · Latitude · Longitude |
| **What you want** | Say "create a sheet for this" or "calculate distances and travel time" |

### For the full UP workbook

Just say: *"Create the UP districts workbook"* — no extra input needed.

### For the HTML map

Just say: *"Give me a downloadable UP district map"* — no extra input needed.

---

## What was built in the original session

This skill was extracted from a live session that produced:

1. An interactive UP district map (HTML, downloadable, works offline)
2. A 4-sheet Excel workbook covering all 75 districts with Census 2011 data
3. A block-level lat/lon sheet (~825 blocks) with Google Maps hyperlinks per block
4. A distance + travel time sheet for Devipatan Division (25 blocks, origin: Balrampur)
5. A distance + travel time sheet for Prayagraj Division (58 blocks, origin: Prayagraj)

---

## Data sources

| Data | Source |
|------|--------|
| District area & population | Census of India 2011 |
| Block (vikas khand) list | Census of India 2011 field data |
| Block coordinates | Approximate HQ centroids (GIS orientation only — not survey-grade) |
| Municipal body areas | Approximate (UP Municipal Directorate records) |
| District boundaries (map) | [guneetnarula/indian-district-boundaries](https://github.com/guneetnarula/indian-district-boundaries) via jsDelivr CDN |

---

## Key constants

```
Road distance = straight-line × 1.3   (UP rural road sinuosity factor)
Travel speed  = 40 km/h               (average for district/state roads)
Earth radius  = 6371 km               (haversine formula)
```

---

## APIs, libraries, and tools used

| Tool/Library | Purpose |
|---|---|
| `openpyxl` (Python) | Build and style all Excel workbooks |
| `math` (Python stdlib) | Haversine great-circle distance calculation |
| `D3.js v7` (cdnjs) | SVG map rendering, projection, choropleth |
| `TopoJSON v3` (cdnjs) | Decode UP boundary topology |
| `guneetnarula/indian-district-boundaries` | UP district boundary TopoJSON |
| `web_fetch` (Claude tool) | Resolve Google Maps short links to extract lat/lon |
| `bash_tool` (Claude tool) | Run Python scripts, recalculate Excel formulas |
| `present_files` (Claude tool) | Deliver output files to the user |
| `recalc.py` (xlsx skill script) | Force-evaluate all Excel formulas before delivery |

---

## Limitations

- Block coordinates are **approximate centroids** — suitable for routing estimates, not land surveys
- Road distances use a **fixed sinuosity factor (1.3)** — actual road distance varies; use Google Maps "Get Directions" link for precise routing
- Travel times assume **40 km/h average** on rural UP roads — highways will be faster, village roads slower
- District boundary GeoJSON is from a public dataset and may differ slightly from official Survey of India boundaries
- Census data is from **2011** — population figures are now ~13 years old

---

## Repository structure

```
up-district-mapper/
├── SKILL.md          ← Claude skill instructions (this is what Claude reads)
├── README.md         ← This file (for humans on GitHub)
└── references/       ← (optional) canonical data tables for district/block data
```

---

## Prompting guide

See `PROMPTING_GUIDE.md` for detailed notes on how to get the best results from Claude when using this skill — including what to specify, what not to assume, and how to avoid hallucination.
