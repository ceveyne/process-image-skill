# Guide: Region Masking

The `mask` tool defines a region of interest on an image by drawing a **cyan bounding box**.

It works **exactly like the `crop` tool**, but instead of cutting the image, it provides **visual feedback** by drawing a mask that shows the selected area. This is essential for non-destructive workflows where you need to define a region for subsequent tools like `inpaint` or `outpaint`.

**⚠️ Plugin Dependency Check:**

- **If you have the `analyse-image` plugin:** Use the **`detect_object` + `mask` workflow**. It is faster and more precise.
- **If you ONLY have the `process-image` plugin:** The **Salami Technique** below is your **primary and most reliable method**. Do not skip it; it is the standard for precise region selection without detection capabilities.

## 🔄 Fallback: The Salami Technique (Sequential Adjustment)

If object detection is unavailable or unreliable, use this iterative manual approach. Avoid changing all four mask parameters simultaneously:

1.  **Left Side:** Adjust `cropLeft` until the left boundary of the mask is perfect.
2.  **Right Side:** Fix the left value and adjust only `cropRight`.
3.  **Top Side:** Fix both side values and fine-tune `cropTop`.
4.  **Finalization:** Use `cropBottom` or `imageFormat` to set the final bottom boundary.

## 🎯 Key Principles for Perfection

### 1. Visual Balance over Numerical Symmetry

Do not assume that symmetric values (e.g., `cropLeft: 30, cropRight: 30`) result in a centered subject. Subjects are rarely perfectly centered in the source image. Always prioritize the **visual gap** between the subject and the mask edge rather than matching the numbers.

### 2. The Power of Tiny Percentages

In extreme close-ups, the margin for error is minimal. A difference of only **1% to 3%** can be the tipping point between a professional tight mask and one that clips the subject. Be patient and iterate in small increments.

### 3. Leveraging `imageFormat`

Instead of manually guessing values for `cropBottom`, perfect the top and sides first, then apply a predefined ratio:

- `'square'` (1:1)
- `'landscape'` (4:3)
- `'portrait'` (3:4)
- `'16:9'`

This ensures a mathematically correct aspect ratio while maintaining your precisely tuned side boundaries.

## 🛠️ Flexible Units & Enhanced Feedback

The `mask` tool supports mixed units for precise control:

- **Default (%):** Numbers without units are treated as percentages (e.g., `cropLeft=20` is 20%).
- **Absolute (px):** You can specify exact pixels by appending 'px' (e.g., `cropRight="150px"`).

**Calibration Aid:** The tool output includes a detailed JSON breakdown showing both the percentage (`pct`) and pixel value (`px`) for every side. This is crucial for understanding the scale.

## 🔗 Workflow Integration

The primary purpose of the `mask` tool is to create a region of interest for other tools:

- **`inpaint`:** Pass the masked image (`iN`) as the `canvas` to repaint only the masked region.
- **`outpaint`:** Use the mask to define the area to be extended.

## 📦 Multi-Box Masking (Multiple Bounding Boxes in One Step)

Both `inpaint` and `outpaint` support **multiple detection regions** in a single call. This allows you to combine several bounding boxes into one unified mask and edit them simultaneously — instead of processing each region sequentially.

### When to Use Multi-Box Masking

- Editing **symmetrical features** (e.g., both eyes, both ears, both hands)
- Applying the **same change** to multiple objects of the same type
- Reducing context usage and inference time by combining steps

### How It Works

After running `detect_object`, pass **arrays** for `detectLabel` and `detectIndex`:

```json
{
  "tool": "inpaint",
  "canvas": "a1",
  "detectLabel": ["eye", "eye"],
  "detectIndex": [0, 1],
  "prompt": "Change the iris color from blue to dark brown"
}
```

**Key Parameters:**

| Parameter     | Type                 | Description                                                                              |
| ------------- | -------------------- | ---------------------------------------------------------------------------------------- |
| `detectLabel` | `string[]`           | Array of labels — one per region to mask                                                 |
| `detectIndex` | `number[]`           | Array of zero-based indices — selects which detection to use when multiple matches exist |
| `frameAdjust` | `number` or `string` | Applied uniformly to **all** regions (e.g., `5` = +5% padding on every box)              |

### Important Rules

1. **All labels must refer to detections on the same source image.** You cannot mix detections from different images.
2. **`detectIndex` is parallel to `detectLabel`.** Each index corresponds to the label at the same position.
3. **`frameAdjust` is applied uniformly.** You cannot set different padding per region — all boxes get the same expansion/shrinkage.
4. **The prompt applies to ALL masked regions.** If you need different edits per region, use separate `inpaint` calls instead.

### Example: Changing Both Eyes Simultaneously

```
Step 1: detect_object → detects two eyes (label: "eye", indices 0 and 1)
Step 2: inpaint with detectLabel=["eye","eye"], detectIndex=[0,1]
        → Both eyes are repainted in a single inference pass
```

**Tested & Verified:** Iris color change from blue to dark brown on both eyes — consistent result, no artifacts, surrounding features (eyelids, skin, lighting) preserved.

### When NOT to Use Multi-Box Masking

- When regions need **different prompts** (e.g., left eye → brown, right eye → green)
- When bounding boxes are **too close together** and risk merging into an unintended larger region
- When you need **different `frameAdjust`** values per region

## 🔄 Feedback & Autonomy Loop

Depending on the visual clarity of the source image and the precision required, choose one of these two paths:

- **Autonomous Refinement:** If your vision system allows for a confident assessment of the gaps and boundaries, proceed with iterative masks independently until the region is visually perfect.
- **User-Guided Refinement:** If there is ambiguity or if you cannot confidently judge the "perfect" region, perform one adjustment at a time and request user feedback before proceeding to the next step.
