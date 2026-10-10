# Guide: Precise Image Cropping

The `crop` tool allows for the removal of percentages from any side of an image.

**⚠️ Plugin Dependency Check:**

- **If you have the `analyse-image` plugin:** Use the **`detect_object` + `zoom_in` workflow** (see [`high-resolution-zooming.md`](high-resolution-zooming.md)). It is faster and more precise.
- **If you ONLY have the `process-image` plugin:** The **Salami Technique** below is your **primary and most reliable method**. Do not skip it; it is the standard for precise cropping without detection capabilities.

## 🔄 Fallback: The Salami Technique (Sequential Adjustment)

If object detection is unavailable or unreliable, use this iterative manual approach. Avoid changing all four crop parameters simultaneously:

1.  **Left Side:** Adjust `cropLeft` until the left boundary is perfect.
2.  **Right Side:** Fix the left value and adjust only `cropRight`.
3.  **Top Side:** Fix both side values and fine-tune `cropTop`.
4.  **Finalization:** Use `imageFormat` to set the final aspect ratio (which automatically handles the bottom crop).

## 🎯 Key Principles for Perfection

### 1. Visual Balance over Numerical Symmetry

Do not assume that symmetric values (e.g., `cropLeft: 30, cropRight: 30`) result in a centered subject. Subjects are rarely perfectly centered in the source image, and head shapes vary. Always prioritize the **visual gap** between the subject and the edge rather than matching the numbers.

### 2. The Power of Tiny Percentages

In extreme close-ups, the margin for error is minimal. A difference of only **1% to 3%** can be the tipping point between a professional tight crop and an "ugly" clip where hair or edges are chopped off. Be patient and iterate in small increments.

### 3. Leveraging `imageFormat`

Instead of manually guessing values for `cropBottom`, perfect the top and sides first, then apply a predefined ratio:

- `'square'` (1:1)
- `'landscape'` (4:3)
- `'portrait'` (3:4)
- `'16:9'`

This ensures a mathematically correct aspect ratio while maintaining your precisely tuned side boundaries.

## 🛠️ Flexible Units & Enhanced Feedback

The `crop` tool now supports mixed units for precise control:

- **Default (%):** Numbers without units are treated as percentages (e.g., `cropLeft=20` is 20%).
- **Absolute (px):** You can specify exact pixels by appending 'px' (e.g., `cropRight="150px"`).

**Calibration Aid:** The tool output now includes a detailed JSON breakdown showing both the percentage (`pct`) and pixel value (`px`) for every side. This is crucial for understanding the scale—e.g., realizing that 10% height might equal 102 pixels, preventing accidental clipping of the subject.

## 🔄 Feedback & Autonomy Loop

Depending on the visual clarity of the source image and the precision required, choose one of these two paths:

- **Autonomous Refinement:** If your vision system allows for a confident assessment of the gaps and boundaries, proceed with iterative crops independently until the result is visually perfect.
- **User-Guided Refinement:** If there is ambiguity or if you cannot confidently judge the "perfect" tight crop, perform one adjustment at a time and request user feedback before proceeding to the next step.
