# draw.io format and safety notes

## File model

- Root container: `<mxfile>` with one or more `<diagram>` pages.
- Each page stores one `<mxGraphModel>`.
- A page can be:
  - uncompressed: `<diagram><mxGraphModel>...</mxGraphModel></diagram>`
  - compressed: `<diagram>...</diagram>` payload text.

## Compression pipeline

draw.io page payload compression is:

1. `encodeURIComponent(mxGraphModel XML)`
2. `deflateRaw(...)`
3. `base64(...)`

Decompression is the exact reverse.

## mxGraph essentials

- Shapes/containers/text blocks: `<mxCell vertex="1" ...>`
- Connectors: `<mxCell edge="1" source="..." target="..." ...>`
- Coordinates and routing live in nested `<mxGeometry as="geometry" ...>`.
- Style string format: `key=value;key=value;...`
  - style names (without `=`) can appear first.

## Safe editing rules

- Prefer targeted edits by `mxCell@id`; avoid broad search/replace over whole file.
- Keep edits non-destructive: write to a new output file by default.
- If editing in place, create a backup first.
- Preserve page storage mode unless explicitly converting.
- Validate after changes:
  - XML parse succeeds
  - compressed pages decode
  - edge `source`/`target` references exist
  - style checks for contrast/label backgrounds when visual edits are involved

## Visual-style safety checks

When changing theme/background/text/arrow styles, explicitly verify:

- `edgeLabel` cells use transparent backgrounds (`fillColor=none`, `labelBackgroundColor=none`).
- text remains readable against fill colors (use contrast checks where parseable).
- connector stroke colors remain visible on page background.
- large container/background cells and dependent text/edge cells are updated consistently.

## Cross-platform theme pitfalls

draw.io can render some style defaults differently across Windows, Mac, and dark-mode-aware builds. Check these first when a diagram suddenly grows dark backgrounds or boxed labels:

- `imageBackground=default` on image/icon cells
- `light-dark(...)` in style values
- `light-dark(...)` or `var(--ge-dark-color, ...)` inside HTML-backed labels (`mxCell@value`)
- inline HTML `background-color:` declarations that can show up as visible boxes
- edge labels without explicit `labelBackgroundColor=none`
- shaped cells with no explicit `fillColor` or `strokeColor`

Recommended triage:

1. Scan raw XML with:
   `rg -n "light-dark|imageBackground=default|var\\(--ge-dark-color|background-color:" diagram.drawio`
2. Run:
   `python3 script validate diagram.drawio --style`
3. If the drift is broad rather than isolated, run:
   `python3 script normalize-light in.drawio out.drawio`

## HTML-backed label editing

Many draw.io visual issues live inside HTML stored in `mxCell@value`, not only in the `style` attribute.

When labels are HTML-backed:

- inspect `value=` for `light-dark(...)`, `var(--ge-dark-color, ...)`, and inline `background-color:`
- prefer replacing theme functions with fixed light-mode values
- remove inline background fills unless they are intentionally visible
- keep text color decisions simple and explicit (`rgb(0, 0, 0)` / `#000000`, or the diagram's chosen accent color)

## Useful UI paths in draw.io

- Edit raw page model: `Extras > Edit Diagram`
- Edit style string for selected shape: right-click shape -> `Edit Style`

## Primary references

- https://www.drawio.com/doc/faq/diagram-source-edit
- https://www.drawio.com/doc/faq/save-file-formats
- https://www.drawio.com/doc/faq/export-to-xml
- https://github.com/jgraph/drawio (Graph.compress / Graph.decompress in source)
- https://jgraph.github.io/mxgraph/docs/js-api/files/model/mxCell-js.html
- https://jgraph.github.io/mxgraph/docs/js-api/files/model/mxGraphModel-js.html
