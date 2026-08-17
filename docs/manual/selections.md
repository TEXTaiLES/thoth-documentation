# Selections

A selection is a model-scoped annotation whose payload identifies mesh faces. THOTH highlights those faces with the selection's color.

## Create and activate a selection

Expand a model in **Scene Structure**, then select the **+** action beside Selections. You can also press `Shift+N`; in that case THOTH uses the model under the pointer, or the first loaded model when no model is under the pointer.

The new selection starts with a generated ID, the name `New Selection`, an empty face set, and a generated color. Select its row before using Brush, Eraser, or Lasso. Only one annotation is active at a time.

Number keys `0` through `9` activate a selection with the corresponding ID. Because IDs are scoped to a model, use the scene tree when scenes contain the same numeric selection ID under multiple models.

## Edit a selection

Use the row controls to:

- edit its name, description, color, visibility, and related records;
- hide or show the highlighted faces; or
- delete it.

The face count shown in the controller is derived from all selected mesh-face IDs. Internally, selections are stored per mesh and exported as compact comma-separated ranges, such as `"1-3,8,11-14"`.

Deleting a selection is reversible with Undo during the current session. A deleted selection is marked as trash in runtime state and omitted from export.

See [Annotation details](annotations.md) for the fields shared with measurements and semantic annotations.
