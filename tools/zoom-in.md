# `zoom-in`

Re-render the cropped canvas region at full resolution via Draw Things image2image.

Workflow: first use `crop` (or `detect_object`) to select the region, then call `zoom-in` on the source canvas. `zoom-in` reads the stored crop metadata automatically — no crop parameters needed.

Alternatively, use `detectLabel` to target a detected object directly without a prior crop step:

- **detectLabel** — label text to match (e.g. `cat`, `a person`) — requires a prior `detect_object` run.
- **detectIndex** — zero-based index when multiple matches exist (default: 0).
- **frameAdjust** — expand (+) or shrink (-) the bounding box before rendering. Number = % of bbox dimensions (preserves aspect ratio); string with 'px' suffix = absolute pixel margin. E.g. `5`, `-3`, `20px`.

`canvas` may be the original source or the annotated `detect_object` result (e.g. `i3`). Example: `canvas=i3, detectLabel=cat, frameAdjust=8`.

Step-by-step guide (detection-based zoom + `frameAdjust` calibration): [`../workflows/high-resolution-zooming.md`](../workflows/high-resolution-zooming.md).

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **canvas** — Source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle instead. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Also accepts a filename in the scratchpad folder or an absolute path.
- **width** / **height** — Output dimensions. Default: derived from canvas aspect ratio.
- **imageFormat** — Target aspect ratio (`square`, `landscape`, `portrait`, `16:9`). Expands the crop region to match the aspect ratio before re-rendering.

Returns: inline preview, JSON with file URLs, crop info, model info, and clickable links.
