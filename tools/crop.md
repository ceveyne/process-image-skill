# `crop`

Crop an image by adjusting one axis at a time.

NOTE — If you have access to `detect_object`: use `detectLabel` to crop directly to a detected object's bounding box.

- **detectLabel** — label text to match (e.g. `cat`, `a person`) — case-insensitive substring match.
- **detectIndex** — zero-based index when multiple matches exist (default: 0).
- **frameAdjust** — expand (+) or shrink (-) the box. Number = % of bbox dimensions (preserves aspect ratio); string with 'px' suffix = absolute pixel margin. E.g. `5`, `-3`, `20px`.

`canvas` may be the original source or the annotated `detect_object` result (e.g. `i3`). Example: `canvas=i3, detectLabel=cat, frameAdjust=5`.

Step-by-step guide (with/without `detect_object`): [`../workflows/precise-image-cropping.md`](../workflows/precise-image-cropping.md).

IMPORTANT — Without `detect_object` (iterative procedure, do NOT set all sides at once):

1. Set `cropLeft` only → inspect result.
2. Keep `cropLeft`, set `cropRight` only → inspect.
3. Keep both, set `cropTop` only → inspect.
4. Set `imageFormat` to lock the aspect ratio (derives `cropBottom` automatically).

At tight crops, 1–3% per step is enough. Symmetric values do NOT guarantee a centred subject — judge by visual gap, not numbers.

## Parameters

- **scratchpadFolder** — Scratchpad session folder name, one level under CHAT_WORKING_DIRECTORIES. How to obtain it, and whether it is required, depends on your client — see SKILL.md's 'Chat working directory' section.
- **canvas** — Source image. An attachment the user added to the chat is referenced by its filename (basename) — or, on clients that support attachment handles (see SKILL.md), by its `aN` handle instead. Generated images, pictures and variants use notations 'i3', 'p1', 'v2'. Also accepts a filename in the scratchpad folder or an absolute path.
- **cropLeft** / **cropRight** / **cropTop** / **cropBottom** — Amount to remove per edge. Default unit: % (0–99). Optionally append `px` for absolute pixels (e.g. `120px` or `10%`).
- **imageFormat** — Target aspect ratio (`square`=1:1, `landscape`=4:3, `portrait`=3:4, `16:9`). Only active when explicitly set. Ignored when all 4 sides are given. Per axis: neither side → centred; one side → anchored, opposite defaults to 0; both → locked.

Examples: `imageFormat=portrait` → centred 3:4 crop. `cropLeft=10, imageFormat=16:9` → anchor left at 10%, centre vertically. `cropLeft=15, cropRight=15, cropTop=10, cropBottom=10` → all 4 explicit, `imageFormat` ignored.

Returns: the cropped image saved to the chat working directory as a new variant.
