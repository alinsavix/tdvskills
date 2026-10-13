# Photoshop implementation notes

Read this when implementing the projection, extraction, or card layout with Photoshop MCP scripting. It records reusable mechanics from the working breakdown; document IDs, layer indices, dimensions, titles, and paths must come from the current source.

## MCP and ExtendScript

- Discover the installed schemas for `photoshop_get_state`, `photoshop_list_documents`, `photoshop_get_layers`, `photoshop_get_preview`, and `photoshop_execute_script`. Pin operations to observed document IDs; multiple helper tabs make the active tab unreliable.
- `photoshop_execute_script` wraps code in a function. Use an explicit `return` for results. Do not assume `JSON` exists in ExtendScript; return plain objects through the MCP's serializer. Supply data literals from the caller if needed.
- ExtendScript is an older JavaScript environment. Prefer `var`, ordinary functions and loops, and `UnitValue.as('px')` for geometry. Keep Photoshop text sizes in points and create RGB `SolidColor` values explicitly.
- Use measured, verified font PostScript names. Use explicit branches for categories, colors, and emptiness; verify the resulting captions in the preview.
- Break a large build into reviewable stages, returning created IDs and completed counts. Set an appropriate script timeout, up to the installed tool's limit. In practice, multi-layer transformations and dozens of presentation layers can take minutes.
- An `extendscript_timeout` ends the MCP wait, not necessarily Photoshop's script. Follow the installed recovery contract: retry ping until it succeeds, then inspect state and existing output. Resume missing work only. Never rerun the whole build merely because the response timed out.

## Content classification and isolation

Capture the initial recursive visibility and source paths before hiding anything in a working copy. Effective visibility includes all ancestors. Stable IDs are preferable to paths if the source structure may change.

Classify layer semantics before applying pixel-empty tests:

| Candidate | Treatment |
| --- | --- |
| Ordinary raster layer, no alpha in the source canvas, no contributing effect | Omit |
| Empty structural group | Omit |
| Hidden alternative or hidden ancestor | Omit by default |
| Visible text, vector/shape, fill, or smart object with rendered content | Include |
| Adjustment or meaningful compositing operation with no own alpha | Include as effect/context card |
| Clipped layer, blend-dependent layer, or inherited group effect | Render in context or show a labeled dependent unit |

Zero-area bounds can identify a straightforward empty raster layer, but are not a general-purpose classifier. Conversely, a broad nominal bounding box does not prove visible pixels. Check actual alpha/rendered output on a disposable copy when ambiguous. Do not delete ambiguous source layers or use destructive inspection on the original.

For independent ordinary layers, a working duplicate can isolate each selected leaf while enabling its ancestors. Select the source canvas and copy merged to obtain its rendered contribution, including masks and applicable styles. Restore the working visibility as needed. Do not use this isolation shortcut for an adjustment or blending operation that requires underlying content.

A masked or styled layer's nominal bounds may differ from the copied pixels. Establish the actual rendered pixel extent and its source location on the disposable copy. Crop/clip consistently to the original canvas; do not derive placement from the destination's arbitrary paste location. If the source contribution is off-canvas and has no contextual effect inside the source canvas, omit it.

Keep a full-resolution assembled reference in a hidden helper group in the breakdown when useful. Duplicate it into cards; do not repeatedly enlarge a previously downsampled thumbnail. Artwork copies can be converted to smart objects before the isometric transforms. `newPlacedLayer` was usable through Photoshop's Action Manager in the tested installation; inspect capabilities and handle tool errors rather than assuming every version behaves identically.

## Common isometric geometry

One useful upright isometric projection has the source x-axis at -30 degrees and the source y-axis vertical. For source canvas width `W`, height `H`, scale `s`, and plane bounding-box origin `(L, T)`:

```text
a = sqrt(3) / 2 * s
screenX(x,y) = L + a*x
screenY(x,y) = T + 0.5*s*W - 0.5*s*x + s*y
planeWidth  = a*W
planeHeight = 0.5*s*W + s*H
```

This keeps all source positions registered. For base-to-top display index `i`, increase `L[i]` from left to right; keep `T` aligned unless a deliberate layout requires otherwise. Derive the four plane corners by mapping `(0,0)`, `(W,0)`, `(W,H)`, `(0,H)`.

A tested native transform sequence for a rendered artwork copy is:

1. Rotate +45 degrees about its center.
2. Resize horizontally by `100 * sqrt(2) * a` percent and vertically by `100 * sqrt(2) * 0.5 * s` percent.
3. Rotate -60 degrees about its center.
4. Translate its center to `screenX(cx,cy), screenY(cx,cy)`, where `(cx,cy)` is the center of its actual source-rendered extent.

Use a smart object to avoid repeated destructive resampling. Check Photoshop's actual transformed bounds and interpolation on a representative layer before applying the same mapping to the entire inventory. Transform boundary and artwork consistently; never normalize each artwork's position independently to a plane center.

If changing an existing layout, move the artwork, plane, label, and related marker coherently. Update the reading-order captions and cards. Reversing x positions alone may introduce overlaps, so inspect lettering and callouts afterward.

## Registered thumbnails and details

For a thumbnail at `(tx,ty)` with width `tw`, use `q=tw/W` and height `q*H`:

```text
thumbnailX(x) = tx + q*x
thumbnailY(y) = ty + q*y
```

Place the assembled reference over the entire thumbnail, typically at 15-25% opacity against a light card. Place the layer at its mapped source coordinates. Use its rendered extent or affected region for the location outline, with a little visual padding if needed. The padding is a display aid, not a new source bounding box.

An isolated graphic enlarged by native scale factor `f` has an enlargement of `f/q` relative to the thumbnail. Label a rounded scale honestly as approximate when rounded. Preserve the detail's aspect ratio. A contextual crop is preferable where an isolated operation would be misleading.

Adjustment examples: a Curves layer can show the underlying composite with and without the adjustment; a masked adjustment can also show its affected area. A clipping-dependent effect must retain its clipping base. If a retained adjustment currently makes no visible difference, label that fact rather than representing it as empty pixel content.

## Presentation and verification

- Thin, faint rear outlines plus stronger front vertical/lower edges usually read better than equally prominent outlines at every crossing.
- Transparent-looking tinted supports should remain faint enough to avoid materially coloring or obscuring other layers.
- Prefer a short numbered pointer next to visible artwork to a long line passing through several planes. Place markers manually from the preview when bounds alone choose a poor anchor.
- Include the same number beside the plane's name and in its location card. After filtering empty layers, rebuild numbering and spacing together.
- Review the full image and at least one enlarged detail card. A temporary flattened duplicate cropped for preview is useful; close that helper without saving afterward and reactivate the breakdown.
- Native Photoshop content and semantic compositing dependencies take precedence over a convenient diagram layout. Note approximations instead of claiming an isolated render exactly reproduces a blend-dependent effect.
