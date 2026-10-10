# `inpaint`

Repaint a masked region of the canvas by re-generating the area inside the mask.

The area inside the mask is changed — the model fills it with new content based on the prompt. The area outside the mask is protected and remains unchanged. The canvas itself is never modified; the result is saved as a new image.

When `canvas` is a prior `mask` result (e.g. `i8`), the mask region is picked up automatically — no extra parameters needed.

Alternatively, use `detectLabel` to target one or more detected objects without a prior mask step:

- **detectLabel** — label or array of labels to match (e.g. `cat`, `["left eye", "right eye"]`) — requires a prior `detect_object` run. Comma-separated string also accepted (multi-word labels supported).
- **detectIndex** — zero-based index or array of indices parallel to `detectLabel` (default: 0 per entry).
- **frameAdjust** — expand (+) or shrink (-) the detection box, applied uniformly to all regions. Number = % of bbox diagonal; string with 'px' suffix = pixels. E.g. `5`, `20px`, `-10%`.

When `detectLabel` is an array, all labels must refer to detections on the same source image. `canvas` may be the original source or the annotated `detect_object` result (e.g. `i3`).

Multi-box masking (combining several regions in one call): [`../workflows/region-masking.md`](../workflows/region-masking.md).

Use `moodboard` to pass one or more reference images (e.g. a logo) that guide what gets generated inside the mask. The model treats them as a style/content reference, not a pixel-exact overlay. For small, filigree regions (e.g. a small logo on fabric), `model: qwen-image-2.1` tends to give the cleanest results.

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **canvas** — Required. Source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle instead. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Also accepts a filename in the scratchpad folder or an absolute path.
- **prompt** — What should appear in the repainted region. Default: empty (preserve content).
- **moodboard** — Reference image(s) to guide the repainted region (e.g. a logo to insert). Array of source notations, same format as `canvas`.
- **model** — Model preset for re-rendering. Default: `auto`. For small, filigree regions, `qwen-image-2.1` tends to give the best results.
- **width** / **height** — Output dimensions. Default: derived from canvas.

Returns: inline preview, JSON with file URLs, model info, and clickable links.
