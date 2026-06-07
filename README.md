[README_ImportStudio.md](https://github.com/user-attachments/files/28675122/README_ImportStudio.md)
# import-studio
unstructured EXCEL data extraction for TGI Studio app
# Import Studio

**Convert messy raw Excel tumor measurements into clean TGI Studio V3 format — in your browser.**

Lab measurement files are rarely clean. Column layouts shift between studies, arm labels have typos, animal IDs pick up letter suffixes, and pre-randomization blocks mix with study days. Import Studio reads those files as-is, lets you visually confirm and correct the structure, and exports a V3 file ready to drop straight into TGI Studio.

No installation. No server. Nothing leaves your machine.

> Part of the **Studio Suite** alongside TGI Studio, Power Studio, and Pipeline Studio.

**© 2026 Alexey Sorokin**

---

## Table of Contents

1. [Quick Start](#quick-start)
2. [Workflow Overview](#workflow-overview)
3. [Step 1 — Upload](#step-1--upload)
4. [Step 2 — Review Structure](#step-2--review-structure)
5. [Step 3 — Export V3](#step-3--export-v3)
6. [V3 Format Reference](#v3-format-reference)
7. [Notes & Tips](#notes--tips)

---

## Quick Start

1. Download `Import_Studio.html`.
2. Open it in any modern browser (Chrome, Edge, Firefox, Safari).
3. Drop your Excel file onto the upload zone.
4. Confirm the auto-detected groups and day-blocks.
5. Click **Review export →** and download.

---

## Workflow Overview

```
Raw Excel  →  [Upload]  →  [Review structure]  →  [Export V3]  →  TGI Studio
```

Three steps, fully visual. Every setting updates the extraction preview in real time.

---

## Step 1 — Upload

Drop any `.xlsx` or `.xls` file containing tumor measurements. The expected layout is a **repeating horizontal block pattern**: each measurement day is a group of columns (Animal ID · Length · Width · Volume · Weight), repeated across the sheet for each timepoint.

Click **🧪 Example** to load a bundled messy example file and explore the workflow.

---

## Step 2 — Review Structure

This is where Import Studio does its work. Everything updates live as you adjust settings.

### Sheet & block detection

| Setting | Default | What it does |
|---------|---------|-------------|
| **Sheet** | First sheet | Which worksheet to read |
| **Header rows to skip** | 1 | Rows at the top to ignore (dates, labels, metadata) |
| **Block width** | 6 | Number of columns per measurement day-block |
| **Column roles** | ID · L · W · V · Wt | Which column in each block maps to which measurement |

The raw sheet renders colour-coded by detected role — teal = ID, blue = Length, green = Width, yellow = Volume, purple = Weight, dimmed = ignored. Block column headers are clickable to set the **Day 1 anchor** (the block that becomes Day 1, with earlier blocks becoming pre-measurements).

### Group / arm assignment

Import Studio auto-detects treatment groups from arm labels ("Arm1", "Vehicle", "Group 3", etc.) and immediately renames them to a clean **Arm 1, Arm 2, Arm 3…** sequence.

**Manual override** — if auto-detection gets it wrong:

- Click **👆 Assign animals → groups** to enter drag mode.
- **Click and drag** down over the rows of one treatment group — the rows highlight teal, locked rows (already assigned) show dimmed red.
- Release to commit. Repeat for each arm.
- Rename each group in the panel that appears (e.g. Arm 1 → Vehicle, Arm 2 → Drug A).
- **↩ Undo last** reverses the most recent drag.
- **↺ Clear groups** resets all assignments.

Once assigned, group boundaries are locked — dragging over an already-assigned row does nothing, preventing accidental re-assignment.

### Day numbering

Per-block day inputs appear below the detected block list. Edit any day number directly; the input switches automatically to manual mode. Clicking a block's column header sets it as **Day 1**, with earlier blocks becoming pre-measurements (shown with a dashed amber "pre" tag).

### Control group detection

Import Studio scans group names for vehicle/control keywords (`vehicle`, `veh`, `control`, `ctrl`, `saline`, `PBS`, `placebo`, `DMSO`, etc.) and marks the matching arm with a **★ control** badge. That arm is placed first in the V3 export — TGI Studio uses the first group as its reference for TGI% calculations. If no keyword matches, Arm 1 is used by convention.

### Duplicate ID detection

Animal IDs are checked for same-day duplicates within a group (likely a typo) and for cross-group duplicates (same ID in two arms). Duplicates are highlighted **red** in the raw grid. Click any ID cell to edit it — the change applies to that animal's ID in every day-block automatically. An **↺ undo** button appears next to each edited ID.

### Options

| Option | Default | Effect |
|--------|---------|--------|
| Exclude pre-measurement blocks | ✓ | Drops blocks with days before Day 1 from the export |
| Recompute Volume from L × W² / 2 | ✓ | Recalculates volume using TGI Studio's formula, ignoring stored values |
| Skip rows missing L and W | ✓ | Drops rows with no usable measurements |
| Clean animal IDs | ☐ | Strips letters and symbols, keeping digits only (e.g. "43325B" → 43325) |
| Auto-detect control group | ✓ | Marks and positions the vehicle/control arm as the TGI reference |

---

## Step 3 — Export V3

A live preview shows the extracted data as a flat table with stats (groups, animals, timepoints, measurements). Duplicate IDs are flagged in red here too.

- **⬇ Download V3 Excel** — exports a `.xlsx` file in TGI Studio V3 format
- **⬇ Download CSV** — same data as comma-separated values

Then open TGI Studio and import the file via the **Import** button.

---

## V3 Format Reference

The V3 format is a flat Excel/CSV with repeating 6-column blocks — one block per timepoint:

```
Group | ID | Day | Length | Width | Weight | Group | ID | Day | Length | Width | Weight | …
Arm 1 | 43307 | 1 | 7.8 | 7.0 | 26.8 | Arm 1 | 43307 | 4 | 7.0 | 6.4 | 26.2 | …
```

- **Row 1** — header: `Group, ID, Day, Length, Width, Weight` repeated per timepoint
- **Rows 2+** — one row per animal, measurements for all timepoints in that row
- Missing measurements are left blank (animal was not measured that day)
- The control/vehicle group appears first

---

## Notes & Tips

- **Messy arm labels** are fine — auto-detect handles "arm1 ", "ARM-3 (8mpk)", "group4", "VEHICLE" etc. and normalises them to Arm 1, Arm 2, … Click **✎ Edit / rename these** to customise.
- **Pre-randomization blocks** — click the first "real" study block's column header to set it as Day 1. Earlier blocks get negative days and are excluded by default.
- **String animal IDs** like "43325B" are preserved unless you tick **Clean animal IDs**, which strips the letter suffix. Useful when the suffix was a data-entry inconsistency rather than a meaningful distinction.
- **Large files** — the raw grid shows up to 80 rows by default. The V3 preview shows all rows (toggle with "Show all N rows").
- All processing is **client-side** — your data never leaves the browser.
- Best viewed on desktop given the wide spreadsheet grid.

---

Import Studio © 2026 Alexey Sorokin
