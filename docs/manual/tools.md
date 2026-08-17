# Tools

Select a tool from the main toolbar or use its [keyboard shortcut](../keybinds/keybinds.md). Selecting another tool deactivates the previous one.

## Selection tools

Brush, Eraser, and Lasso require an active [selection](selections.md). Choose a selection in the scene tree before editing faces.

### Brush (`B`)

Hold the left mouse button and move over the model to add faces to the active selection. **Size** changes the spherical selector radius. Use `[` and `]` for stepwise size changes.

### Eraser (`E`)

Hold the left mouse button and move over selected faces to remove them. Eraser shares the Brush **Size** control.

### Lasso (`L`)

Draw a freehand polygon around the faces to add. Its options are:

- **Pixel precision**: higher values sample the drawn polygon more frequently and cost more processing time.
- **Normal threshold**: controls how closely a face must point toward the camera. `-1` is most tolerant and `+1` is most restrictive.
- **Select occluded faces**: includes qualifying faces hidden behind visible geometry.

### Subtractive use

The right mouse button reverses the active selection action: Brush removes, Eraser adds, and Lasso subtracts. This applies only while an active selection exists.

### No Tool (`N`)

Deactivates all editing tools and restores normal scene navigation.

## Point tools

### Measure (`M`)

Choose a distance mode, then select two surface points on the same model. THOTH opens a details dialog before it saves the measurement. See [Measurements](measurements.md).

### Semantic Annotation (`A`)

Select one surface point, complete the details dialog, and save. See [Semantic annotations](semantic_annotations.md).

## Temporarily navigate

Editing tools take control of pointer input. Hold `Space` to pause the active tool and navigate the scene; release it to resume. Starting navigation clears an unfinished lasso, measurement, or semantic-annotation point.
