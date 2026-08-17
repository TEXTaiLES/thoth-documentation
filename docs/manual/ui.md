# User interface

THOTH keeps scene content on the right and editing tools around the main 3D view. Controls that change data remain visible when signed out, but selecting one opens the sign-in dialog.

## Top toolbar

- **TEXTaiLES** opens the project website.
- **Add model** opens the model picker. You can select more than one available model before confirming.
- **Settings** contains light and dark UI themes.
- **Info** opens this documentation.

## Scene structure

The tree on the right is organized by model. Expand a model to inspect:

- **Selections**: annotated sets of mesh faces;
- **Semantic Annotations**: annotations anchored to one surface point;
- **Measurements**: saved distances and their endpoints;
- **Artefact**: title, source URL, description, owner, keywords, and copyright;
- **Transforms**: model translation and rotation;
- **Metadata**: schema-driven model metadata; and
- **Sensors**: a reserved model-scoped placeholder. Sensor entries are preserved in scene JSON, but this version does not provide an editing or visualization panel for them.

The model row provides actions to focus the camera, export or download model data, and delete the model. Annotation rows provide actions to edit details, toggle visibility, and delete the item. Use the **+** action beside Selections to create a selection for that model.

## Main toolbar

The main toolbar is arranged in three groups:

1. selection tools: Brush, Eraser, Lasso, and No Tool;
2. point tools: Measure and Semantic Annotation; and
3. history: Undo and Redo.

Selecting a tool opens its options near the toolbar. Only one editing tool is active at a time. See [Tools](tools.md) for interaction details.

## User and export controls

The user button in the upper-right opens sign-in or sign-out controls. After authentication, **Export changes** opens scene persistence and download choices. See [Sign in](../scene/login.md) and [Export and download](export.md).
