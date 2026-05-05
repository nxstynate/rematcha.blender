# ReMatcha

<p align="center">
  <img src="rematcha-logo.png" alt="ReMatcha logo" width="280">
</p>

<p align="center">
  <em>Regex-based material replacement and cleanup for Blender.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Blender-4.0%2B-orange?logo=blender&logoColor=white" alt="Blender 4.0+">
  <img src="https://img.shields.io/badge/version-1.2.0-9bb84e" alt="Version 1.2.0">
  <img src="https://img.shields.io/badge/license-GPL--3.0-blue" alt="License GPL-3.0">
</p>

---

## What it does

ReMatcha is a Blender N-panel addon for finding and replacing materials in bulk using regex patterns, plus cleaning up unused materials from your blend file. It's built for the kind of project where you've imported geometry from a dozen sources and ended up with `Material.001`, `Material.001.001`, `defaultmaterial_oak_v2_FINAL`, and 47 other variants — all of which should really just be one material.

It lives in the 3D Viewport sidebar under the **ReMatcha** tab and is organized into three collapsible sections:

- **Find Materials** — search the blend file with a regex pattern
- **Replace Materials** — swap matched materials for a target material
- **Material Cleanup** — find and remove materials with zero assignments

## Features

- **Regex search** across all materials in the blend file (case-insensitive)
- **Library-aware** — distinguishes local materials from those linked from external `.blend` libraries, and shows the library filename
- **Usage counts** displayed for every match, so you can see how many slots a material actually fills before replacing it
- **Material previews** rendered inline in the list (no more guessing what `wood_03.001.002` looks like)
- **Bulk selection helpers** — All / None / Local-only / Linked-only
- **Modal replacement** with a progress bar and ESC-to-cancel, so big jobs don't lock up the UI
- **Replacement log** showing exactly which materials were swapped and how many slots were affected
- **Unused material cleanup** with confirmation prompt and protection against accidentally removing linked materials

## Installation

1. Download `rematcha.py` from this repository (or the latest release).
2. In Blender, open **Edit → Preferences → Add-ons → Install...** and select the file.
3. Enable the **Material: ReMatcha** checkbox.
4. The panel will appear in the 3D Viewport sidebar (press `N` to open it) under the **ReMatcha** tab.

Requires Blender **4.0 or newer**.

## Usage

### Finding and replacing materials

1. Open the **Find Materials** sub-panel and enter a regex pattern. A few examples:
   - `^wood` — anything starting with `wood`
   - `\.0\d+$` — Blender's auto-numbered duplicates like `.001`, `.012`
   - `oak|maple|birch` — any of these three substrings
   - `^MAT_` — your studio's prefix convention
2. Click the magnifying-glass button. Matches appear in the list with their source (Local or library filename) and usage count.
3. Tick the materials you want to replace. The **All / None / Local / Linked** buttons help with bulk selection.
4. Open the **Replace Materials** sub-panel and pick a target material from the dropdown.
5. Click **Replace Mat**. Progress shows in the status bar; press ESC to cancel mid-run.

The replacement reassigns every material slot using a matched material to point at the target instead. The original materials remain in the blend file (with zero uses) — use the cleanup panel to remove them.

### Cleaning up unused materials

1. Open the **Material Cleanup** sub-panel and click **Find Unused Materials**.
2. Review the list. Linked materials cannot be removed and are flagged accordingly.
3. Select what you want to delete and click **Remove Selected**. Confirm the prompt.

A double-check runs at delete time — if a material picked up assignments since the scan, it'll be skipped rather than yanked out from under you.

## Why regex?

Most cleanup workflows in Blender involve clicking through `Material.001`, `Material.002`, etc. one at a time, or writing a throwaway Python script. Regex lets you describe the *pattern* once — `^Material\.\d+$` — and act on every match at once. It's the difference between manually finding 80 duplicates and writing one expression that catches all of them.

The search is case-insensitive by default, so `oak`, `OAK`, and `Oak` all match the same pattern.

## Tips

- The **Refresh** button in the Find panel re-counts usage if you've been making changes between operations.
- If a target material is in your selection set, ReMatcha will refuse the replacement — a swap to itself would be a no-op and likely a mistake.
- For libraries with deeply nested asset hierarchies, the source column shows just the library *filename* (not the full path), which keeps the list scannable.

## Compatibility

| Blender version | Status      |
|-----------------|-------------|
| 4.0+            | Supported   |
| 3.x             | Untested    |
| 2.93 and older  | Won't work  |

The addon uses the modern `bpy.props` PointerProperty/CollectionProperty API and the 4.x `progress()` UI element.

## Roadmap

Open to contributions — some things on the list:

- Optional regex flags (multiline, exact case-sensitive)
- Save/load named regex patterns across sessions
- Replace by **substitution pattern** rather than a fixed target material (e.g. `^old_` → `new_`)
- Merge-by-pattern (collapse N matches into the first match instead of needing a target)

## License

GPL-3.0 — same license as Blender itself, since this is a Blender addon and gets distributed alongside it.

## Author

NXSTYNATE — built for production rendering pipelines where material chaos is the norm.
