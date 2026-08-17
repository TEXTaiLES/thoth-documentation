# Semantic annotations

A semantic annotation attaches descriptive information to one point on a model surface.

To create one:

1. Select the Semantic Annotation tool or press `A`.
2. Select a point on the model.
3. Enter a name and any description or related records.
4. Choose **Create semantic annotation**.

THOTH adds a surface marker and label and stores the point under the selected model. Use its scene-tree row to select it, edit details, toggle visibility, or delete it.

The point is stored in model-local coordinates, so it follows model translation and rotation. Canonical export includes its `x`, `y`, and `z` coordinates and the selected face ID when available.

See [Annotation details](annotations.md) for the fields shared by all annotation types.
