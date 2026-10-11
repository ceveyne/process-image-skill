---
name: process-image
description: Crop, mask, zoom-in, inpaint, and outpaint images using the image pipeline. Made for LM Studio Bionic.
---

# Image Pipeline

One subfile per tool under `tools/`. File name = tool name + `.md`. Each file contains the full tool description and all parameter descriptions. Open only the file(s) for the tool(s) you are about to call in this session; use `search_file_line` in `tools/` to locate details (e.g. a parameter name).

This skill documents only the tools of the `process-image` plugin. The other image-pipeline plugins (`generate-image`, `analyse-image`, `find-images`) each ship their own separate skill with the same structure — if one of them is not installed, its tools simply don't appear in your tool list.

## Tools

| Tool       | File                                        | Search keywords                                                   |
| ---------- | --------------------------------------------- | -------------------------------------------------------------------- |
| `crop`     | [`tools/crop.md`](tools/crop.md)             | cropLeft/cropRight/cropTop/cropBottom, imageFormat, detectLabel     |
| `mask`     | [`tools/mask.md`](tools/mask.md)             | bounding box, cyan mask, multi-region, detectLabel, detectIndex    |
| `zoom-in`  | [`tools/zoom-in.md`](tools/zoom-in.md)       | re-render, full resolution, crop metadata, detectLabel             |
| `inpaint`  | [`tools/inpaint.md`](tools/inpaint.md)       | repaint masked region, prompt, model                                |
| `outpaint` | [`tools/outpaint.md`](tools/outpaint.md)     | extend canvas, repaint outside mask, prompt, model                  |

## Workflows

Task-oriented guides beyond the per-tool parameter reference, under `workflows/`: [`precise-image-cropping.md`](workflows/precise-image-cropping.md) (crop without detection — the Salami Technique), [`region-masking.md`](workflows/region-masking.md) (mask + multi-box regions for `inpaint`/`outpaint`), [`high-resolution-zooming.md`](workflows/high-resolution-zooming.md) (`zoom-in` + detection-based framing).

## Chat working directory

`scratchpadFolder` is required. Before calling a tool: call `get_scratchpad_folder`, then pass its return value unchanged as `scratchpadFolder` — e.g. if `get_scratchpad_folder` returns "1a2b3c", call the tool with `scratchpadFolder="1a2b3c"`.

## Attachment references

Attachments the user added to the chat are referenced by their filename (basename) in the scratchpad folder. This client does not support `aN` attachment handles.

## Related skills

**Required reading before presenting any result:** `image-display` (installed alongside this plugin) is the backbone for every image/video workflow on this client — it explains how to verify whether a generated/edited image is actually visible to you and to the user, which is not automatic and not the same thing.
