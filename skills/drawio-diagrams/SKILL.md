---
name: drawio-diagrams
description: Read, validate, and edit draw.io diagrams (.drawio) at the XML level — page summaries, filtered cell discovery, targeted edits by mxCell id, batch style updates, theme normalization, and storage conversion.
---

# Drawio Diagrams

## Overview

Use this skill to safely automate draw.io diagram work without relying on manual GUI edits. Use bundled commands for page summaries, filtered cell discovery, validation, theme transforms, storage conversion, and targeted/batch cell edits.

Use this skill especially when a diagram renders differently across platforms (for example Windows vs Mac) or when dark-mode/theme defaults introduce unexpected backgrounds, icon fills, or label boxes.

## Quick start

1. Inspect structure first.
2. Scan for theme-sensitive tokens before editing.
3. Discover candidate cells with filtered `list-cells`.
4. Edit into a new output file (or in-place + `--backup`).
5. Validate structure and style.
6. Convert storage mode only when needed.

```bash
python3 script summary <file.drawio>
rg -n "light-dark|imageBackground=default|var\\(--ge-dark-color|background-color:" <file.drawio>
python3 script list-cells <file.drawio> --page 1 --vertex --min-width 400
python3 script validate <file.drawio> --style
```

## Core workflow

1. Run `summary` to identify page names, storage mode, and cell counts.
2. Scan the file for theme-sensitive tokens with `rg` and targeted `list-cells` queries before editing.
3. Run `list-cells` to find target ids by page/type/style/geometry.
4. Apply targeted changes with `set-cell` or batch changes with `set-cells`.
5. If the issue is cross-platform or light/dark rendering drift, run `normalize-light` before manual cleanups.
6. For large neutral-color shifts, use `apply-theme --theme light|dark`.
7. Run `validate --style` on the edited output.
8. Optionally run `convert-storage` if the output must be all compressed or all uncompressed.

## Commands

### `summary`

Show page-level structure and counts.

```bash
python3 script summary diagram.drawio
python3 script summary diagram.drawio --json
```

### `list-cells`

List cells with optional filters so edits can be scoped precisely.

```bash
python3 script list-cells diagram.drawio --page 5 --vertex --min-width 500
python3 script list-cells diagram.drawio --style-contains fillColor=#000000 --json
python3 script list-cells diagram.drawio --edge --value-contains "Metrics"
```

### `validate`

Validate that pages decode and edge references remain consistent. Optional style lint checks cover contrast, edge label background hygiene, and theme-compatibility risks.

```bash
python3 script validate diagram.drawio
python3 script validate diagram.drawio --style
python3 script validate diagram.drawio --style --fail-on-style
```

Validation checks:

- XML parses correctly
- each page payload decodes/parses
- duplicate `mxCell` ids per page
- edge `source`/`target` ids exist on the same page
- optional style warnings (`--style`):
  - low text contrast where parseable colors exist
  - non-transparent edge-label backgrounds
  - edge labels missing explicit `labelBackgroundColor=none`
  - edge labels missing explicit `fontColor`
  - `light-dark(...)` in style attributes
  - `light-dark(...)` or `var(--ge-dark-color...)` inside HTML label content
  - inline HTML `background-color:` declarations that can render as boxes on some platforms
  - `imageBackground=default`
  - transparent `text` cells with no explicit `fontColor`
  - shaped cells with no explicit `fillColor`/`strokeColor` that may inherit platform theme defaults

### `set-cell`

Edit a single `mxCell` by id (optionally limit to one page).

```bash
python3 script set-cell in.drawio out.drawio \
  --cell-id k_x_UaeQuVGz606EVGA7-25 \
  --append-value " [Updated]"

python3 script set-cell in.drawio out.drawio \
  --cell-id k_x_UaeQuVGz606EVGA7-25 \
  --set-style fontSize=24 \
  --set-style fontColor=#003366 \
  --remove-style gradientColor

python3 script set-cell in.drawio out.drawio \
  --cell-id k_x_UaeQuVGz606EVGA7-25 \
  --set-style fontSize=24 \
  --dry-run

python3 script set-cell in.drawio in.drawio \
  --cell-id k_x_UaeQuVGz606EVGA7-25 \
  --set-style fillColor=#ffffff \
  --in-place --backup
```

### `set-cells`

Batch-edit multiple ids in one atomic write.

```bash
python3 script set-cells in.drawio out.drawio \
  --cell-id id-1 --cell-id id-2 --cell-id id-3 \
  --set-style fillColor=#ffffff \
  --set-style fontColor=#000000

python3 script set-cells in.drawio in.drawio \
  --cell-id id-1 --cell-id id-2 \
  --set-style strokeColor=#000000 \
  --in-place --backup
```

### `apply-theme`

Apply a neutral light/dark transform to selected pages (background + neutral fills/strokes/fonts + edge label cleanup).

```bash
python3 script apply-theme in.drawio out.drawio --theme light
python3 script apply-theme in.drawio in.drawio --theme dark --page 2 --in-place --backup
python3 script apply-theme in.drawio out.drawio --theme light --dry-run
```

### `normalize-light`

Normalize theme-sensitive content for light-mode and cross-platform rendering. This is the fastest first pass when a diagram looked fine on Windows but started showing dark backgrounds or boxed labels on Mac.

What it does:

- sets page backgrounds to `#ffffff`
- resolves `light-dark(...)` and `var(...)` to fixed light-mode values
- strips inline HTML `background-color:` declarations from label content
- converts `imageBackground=default` to `imageBackground=none`
- makes edge labels explicitly transparent (`labelBackgroundColor=none`, `fillColor=none`, `strokeColor=none`)
- assigns `fontColor=#000000` to transparent `text` labels that do not already define one

```bash
python3 script normalize-light in.drawio out.drawio
python3 script normalize-light in.drawio in.drawio --in-place --backup
python3 script normalize-light in.drawio out.drawio --page 2 --dry-run
```

### `convert-storage`

Convert page storage mode across the file.

```bash
python3 script convert-storage in.drawio out.drawio --storage compressed
python3 script convert-storage in.drawio out.drawio --storage uncompressed
python3 script convert-storage in.drawio in.drawio --storage compressed --in-place
python3 script convert-storage in.drawio in.drawio --storage compressed --in-place --backup
```

## Editing rules

- Prefer targeted edits by `mxCell@id`.
- Scan for `light-dark`, `imageBackground=default`, `var(--ge-dark-color`, and inline `background-color:` before visual edits.
- Avoid broad search/replace on full XML.
- Prefer writing to a new output path; if editing in-place, use `--backup`.
- Use `--dry-run` before high-impact edits.
- Use `--force` only when intentionally replacing an existing output file.
- Preserve page storage mode unless conversion is required.
- Validate after each edit batch (`validate --style` for visual changes).
- When labels use HTML in `@value`, inspect and edit both the style string and the HTML payload; visual bugs often live in the HTML, not only in `style=...`.

## Visual Refactor Checklist

Use this checklist for broad style changes (background/theme/contrast updates):

1. Enumerate affected pages with `summary`.
2. Scan for theme-sensitive tokens with `rg` and `list-cells`:
   `light-dark`, `imageBackground=default`, `var(--ge-dark-color`, `background-color:`
3. Locate candidate containers via `list-cells` geometry filters (`--min-width`, `--min-height`).
4. Locate dependent labels/arrows via `list-cells` filters (`--edge`, `--style-contains`, `--value-contains`).
5. If the issue is broad light-mode drift, run `normalize-light --dry-run` first.
6. Run edits in one batch (`set-cells`) where possible; avoid many single writes.
7. Use `--dry-run` first.
8. If in-place, enable `--backup`.
9. Run `validate --style`.
10. Re-open diagram and visually verify target page(s).

## Theme Compatibility

Use this workflow when a diagram changed appearance after moving between Windows and Mac or after draw.io started honoring dark-mode defaults:

1. Run `summary` and confirm page storage/backgrounds.
2. Scan the raw file:
   `rg -n "light-dark|imageBackground=default|var\\(--ge-dark-color|background-color:" diagram.drawio`
3. Run focused queries:
   `python3 script list-cells diagram.drawio --style-contains imageBackground=default`
   `python3 script list-cells diagram.drawio --value-contains "light-dark("`
4. Run `validate --style` to surface theme-risk warnings.
5. Run `normalize-light` for a broad light-mode cleanup.
6. Apply targeted `set-cell`/`set-cells` edits for any remaining exceptions.

Common offenders:

- `imageBackground=default` on icon/image cells
- `light-dark(...)` in style values or HTML labels
- `var(--ge-dark-color, ...)` in HTML labels
- edge labels without `labelBackgroundColor=none`
- `text` labels with transparent fill and no explicit `fontColor`
- shaped cells without explicit `fillColor`/`strokeColor`
- HTML spans with inline `background-color:` that show up as boxes on another platform

## References

Read [references/format-and-safety.md](references/format-and-safety.md) when you need details on:

- draw.io compression/decompression pipeline
- mxGraph model semantics
- UI paths for manual XML/style editing
- authoritative source links
