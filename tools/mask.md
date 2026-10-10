# `mask`

Define a region of interest on an image by drawing a CYAN bounding box.

NOTE — If you have access to `detect_object`: use `detectLabel` to place the mask directly on a detected object's bounding box.

- **detectLabel** — label or array of labels to match (e.g. `"cat"`, `["left eye", "right eye"]`) — case-insensitive substring match. Comma-separated string also accepted (multi-word labels supported).
- **detectIndex** — zero-based index or array of indices parallel to `detectLabel` (default: 0 per entry). Accepts bracket notation: `"[2, 4, 7]"`.
- **frameAdjust** — expand (+) or shrink (-) each bbox uniformly. Number = % of bbox diagonal; string with 'px' suffix = pixels. E.g. `5`, `-3`, `"20px"`.

When `detectLabel` is omitted and N > 1 detections exist for a label, all are auto-expanded (Option A). When `detectLabel` is a single string and `detectIndex` lists multiple indices, masks for each index are generated (Option B). All labels must refer to detections on the same source image.

Step-by-step guide (single + multi-box regions): [`../workflows/region-masking.md`](../workflows/region-masking.md).

`canvas` may be the original source or the annotated `detect_object` result (e.g. `i3`). Example: `canvas=i3, detectLabel=["cat", "dog"], frameAdjust=5`.

## Per-region crop overrides (multi-region only)

`cropLeft`/`cropRight`/`cropTop`/`cropBottom` each accept a SCALAR or an ARRAY parallel to `detectLabel`:

- Scalar: the same override is applied to every region.
- Array: one entry per region, in the same order as `detectLabel`/`detectIndex`. Use `null` for any entry to keep that region's raw detection bbox (no override for that side). The array may be shorter than the label list — missing trailing entries behave like `null`.

WARNING — cropX values are IMAGE-relative (% from that image border), not mask-relative: `cropBottom=30` means "place the bottom edge of the mask 30% up from the bottom of the image". Whether this grows or shrinks the mask depends on where the detection was:

- detection `cropBottom=70` (face near top) + `cropBottom=30` → mask GROWS downward
- detection `cropBottom=10` (face near bottom) + `cropBottom=30` → mask SHRINKS upward

For predictable expand/shrink relative to the detection bbox, use `frameAdjust` instead.

Example (pull the bottom edge of the middle mask to 10% from the image bottom): `detectLabel=["cat", "dog", "bird"], cropBottom=[null, 10, null]` → cat and bird keep their detection bbox; dog's bottom edge is set to 10% from bottom. `imageFormat` is scalar only and applies to single-region masks; ignored in multi-region mode.

IMPORTANT — Without `detect_object` (iterative procedure, do NOT set all sides at once):

1. Set `cropLeft` only → inspect result.
2. Keep `cropLeft`, set `cropRight` only → inspect.
3. Keep both, set `cropTop` only → inspect.
4. Set `imageFormat` to lock the aspect ratio (derives `cropBottom` automatically).

At tight crops, 1–3% per step is enough. Symmetric values do NOT guarantee a centred subject — judge by visual gap, not numbers.

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **canvas** — Source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle instead. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Also accepts a filename in the scratchpad folder or an absolute path.
- **cropLeft** / **cropRight** / **cropTop** / **cropBottom** — Distance from that image border in % (or append `px`). IMAGE-relative: values are % of image dimension from that border. Valid range 0–100 (left+right and top+bottom must each stay below 100). Negative values are invalid. Scalar or per-region array (`null` = keep detection value). For relative expand/shrink use `frameAdjust`.
- **imageFormat** — Target aspect ratio (`square`=1:1, `landscape`=4:3, `portrait`=3:4, `16:9`). Only active when explicitly set. Ignored when all 4 sides are given.

The result is saved as an `iN` image with `cropLeft`/`cropRight`/`cropTop`/`cropBottom` metadata. When `detectLabel` produces multiple regions, all bounding boxes are stored (`bboxes[]`) and all drawn in cyan. Pass it to `inpaint` or `outpaint` as `canvas` to use the marked region(s) as the mask.

Returns: the annotated image saved to the chat working directory as a new `iN` image.
