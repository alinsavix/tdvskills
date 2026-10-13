---
name: photoshop-breakdown
description: Create or revise an editable exploded isometric breakdown of a layered Photoshop document using the Photoshop MCP, with numbered artwork, exact-position location cards, and enlarged small details. Use when the user wants to demonstrate how a PSD is assembled from layers.
---

# Photoshop Breakdown

Build a readable explanation of the user's actual layer structure in Photoshop. Use the connected Photoshop MCP and real document content; do not regenerate or approximate the artwork with image generation.

## Compatibility

This skill has only been tested with [alisaitteke's Photoshop MCP](https://github.com/alisaitteke/photoshop-mcp). It may work with other Photoshop MCP servers, but compatibility has not been verified. Tool names, scripting support, and error handling may need adaptation.

## Default result

- A horizontal exploded stack of upright isometric planes: **bottommost included layer on the left, topmost on the right**.
- A numbered marker beside each graphic, connected by a short leader. Match the number on the plane, its name label, and its location card.
- A flat location guide below the stack. Each card shows the complete assembled image faintly and the current layer at full strength in its original image coordinates, with an outlined location box.
- Enlarged insets for details too small to recognize at the intended viewing size. Link each inset to its location box and state the enlargement relative to the thumbnail.
- A small assembled reference, restrained typography, subtle tinted planes, faint rear edges, and stronger front edges.

Adapt dimensions, spacing, colors, and card layout to the source. These are defaults, not a fixed poster template. Follow explicit user revisions.

## Inspect and select content

1. Verify the MCP connection once per session. Read current state, open documents, a preview, and the recursive layer tree. Pin subsequent operations to the observed document ID. Do not infer the source from the active tab after creating helper documents.
2. Record each candidate's stable source identity/path, original stacking order, ancestor visibility, kind, bounds, masks, clipping relationships, blend mode, opacity, and group context. Inspect descriptors when the normal layer listing is insufficient.
3. Include effectively visible content by default. Exclude hidden alternatives unless requested. Expand groups to meaningful leaf layers; a structural group is not an extra sheet duplicating its children. Keep dependencies together when separating them would misrepresent their effect, and label the combined unit.
4. **Omit empty pixel layers.** A raster layer with no pixels in the displayed source canvas and no meaningful effect does not get a plane, card, placeholder, or number. Skip empty structural groups too. Filter before numbering and layout; do not reserve empty slots.
5. **Do not equate missing pixel bounds with emptiness.** Retain adjustment layers, fill layers, vector content, and other visible non-raster operations that affect or define the composite. A masked or clipped layer may require a contextual render to establish its contribution. If content type or effect is uncertain, inspect it rather than silently dropping it.
6. Assign consistent, contiguous display numbers to the included units in left-to-right order. Preserve original names in labels and retain the mapping to source layers. Do not rename or delete source layers to make the diagram.

## Build the explanation

Create a separate breakdown document unless the user explicitly asks to alter the source. Revise an existing breakdown in place when that is the current task. Preserve source content and use named groups for each plane, labels, guide cards, reference, and presentation elements.

Before writing custom JSX, read [references/photoshop-implementation.md](references/photoshop-implementation.md). Use a matching native MCP tool or recipe when available; use custom scripting for the coordinated projection and layout when no tool covers it.

### Exploded stack

Use one common projection and source coordinate system for every layer. Separation changes a plane's origin, not the registration of content inside it. Show the original canvas boundary even when a layer contains only a tiny detail. Work from rendered copies or smart objects so text, masks, and source content remain available in the source document.

Keep the stack horizontal, with the base on the left. Allow extra spacing where artwork or annotations collide. Avoid applying full-strength opaque backing panels that hide neighboring layers. Quiet crossing outlines; emphasize only the edges needed to identify each sheet.

Anchor markers to recognizable visible pixels when possible, not blindly to a bounding-box corner or the bottom of a large transparent area. Choose short leader routes and keep markers clear of neighboring artwork. Matching numbers should establish identity without making the user trace a long line through many planes.

### Exact-position cards

Use the same complete source canvas, aspect ratio, and coordinate mapping in every card. Keep the assembled reference faint, then overlay the selected content at the correct position and scale. Never center isolated content in its card as a substitute for registration.

Use a card grid when one long strip would make thumbnails too small. Keep card reading order consistent with the stack. A full-canvas background may look blank; label it as the background and show its canvas extent.

For adjustments and similar operations, use an effect card with a clearly labeled contextual before/after comparison, affected region or mask when useful, and a short description of the operation. Do not invent an isolated bitmap for a purely compositing operation. Preserve clipping, masks, and the applicable underlying composite in the comparison.

Enlarge small details only where it improves identification. Use the actual pixels or contextual affected region, a quiet contrasting inset background, and a connector from the thumbnail's location box. Make the inset's enlargement clear so it cannot be mistaken for the element's original size or position.

## Verify and finish

- Confirm included units match the filtered source inventory. No empty pixel-layer placeholders; meaningful adjustments retained; hidden alternatives excluded by default.
- Verify bottom-to-top order runs left to right and all markers, names, and cards agree.
- Inspect the full composition and a representative detail card at readable size. Check placement against the assembled image, inset content, text overlap, connector endpoints, clipping, and excessive plane overlap.
- Fix demonstrated readability issues before delivering. Remove only helper documents created for this run, leaving the requested result active in Photoshop.
- Report what was created and whether it is saved. Save/export according to the user's request; do not overwrite the source PSD implicitly.

Keep progress updates short. If Photoshop continues processing after an MCP timeout, wait for the connection to become responsive before inspecting or editing again; do not launch a duplicate build.
