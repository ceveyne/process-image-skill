# Guide: High-Resolution Zooming

The `zoom-in` tool is used to re-render a previously cropped region of an image at full resolution using Draw Things image2image mode. This process transforms a simple crop (which is just a pixel reduction) into a high-fidelity image by intelligently filling in the details.

## ⚙️ Workflow & Usage

The `zoom-in` tool re-renders a region of an image at full resolution using Draw Things image2image mode. **The recommended workflow** is to pair it with `detect_object` for precise, non-destructive cropping.

### Primary Workflow: Detection-Based Zoom

1.  **Detect:** Run `detect_object` on the source image to identify the target (e.g., `human face`, `person`).
2.  **Zoom:** Call `zoom_in` with `canvas` set to the **original source** and `detectLabel` set to the target.
3.  **Adjust:** Use `frameAdjust` to control the "breathing room" around the object (e.g., `+60` for a safe portrait including all hair, `+20` for a tight crop).
4.  **Refine:** If the result is too tight or too loose, adjust only the `frameAdjust` value and re-run `zoom_in`. No manual percentage guessing is required.

### Legacy Workflow: Manual Crop

If detection is not used, `zoom-in` can operate on manually cropped regions. This is less precise and requires iterative `crop` calls first.

## 🛠️ Model Selection

There are currently four model presets available for `zoom-in`. Depending on the desired aesthetic and detail level, you can specify one of the following in the `model` parameter:

- `auto` (Default)
- `qwen-image`
- `z-image`
- `flux`

## 📊 Detailed Execution Feedback

The tool provides a detailed JSON report of the zoom operation, including the `zoom_input` object. This confirms exactly which crop parameters were applied to the source canvas, allowing you to verify the framing logic.

## 💡 Key Principles & Calibration

### 1. Mandatory Visual Verification (`review_image`)

**Never assume the result is correct.** Agents often hallucinate details or rely on expectations rather than visual reality.

- **Always** use `review_image` on the generated output (`iN`) before proceeding or finalizing.
- Check specifically for the **strict constraints** of the prompt (e.g., "no hair cut", "tight crop"). A "good looking" image is not necessarily a "precise" crop. If the prompt demanded a tight face crop and the result shows too much shoulder, it is a failure of precision, even if the image quality is high.

### 2. `frameAdjust` is Your Primary Control

Instead of guessing `cropLeft/Right/Top/Bottom`, you now only adjust `frameAdjust`. This value scales the detected bounding box.

- **Tight Crop:** `+10` to `+20` (Risk: clipping hair/edges).
- **Standard Portrait:** `+30` to `+50`.
- **Safe Margin (Hair/Flyaways):** `+50` to `+70`. (Test showed `+60` was ideal for a full-framed portrait with fine hair strands).
- **Detail Zoom:** `-5` to `-15`.

### 3. JSON Feedback for Calibration

The tool output includes a `zoom_input` JSON block. This confirms exactly which crop percentages were applied. If you find that `+50` consistently clips the subject in your specific image style, note the resulting percentages and increase your baseline (e.g., to `+60`).

### 4. Model Selection

Since `zoom-in` uses an image-to-image re-rendering process, results may vary between models. It is recommended to test different models on the same crop to determine which one best preserves the identity and texture of your specific subject.
