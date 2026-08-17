# Models

Each scene contains a `models` object keyed by stable model ID. Every model owns its artefact description, transforms, metadata, selections, measurements, semantic annotations, and sensor references.

## Add models

Select **Add model** or press `Shift+A`. The picker lists resources available from the active backend:

- local mode lists models in the authenticated ATON user's model storage; and
- HESTIA mode lists HESTIA artefacts exposed by the configured gateway.

THOTH accepts model entries whose resource path ends in `.glb`, `.gltf`, or `.obj`. Select one or more entries and choose **Add models**. THOTH resolves each resource URL and its artefact details, creates the model record, loads the geometry, and focuses the first model when the initial scene finishes loading.

## Scene-tree actions

- **Focus** moves the camera to the model.
- **Export model changes** downloads a model-focused artefact-data JSON file. A backend export option is shown only when the configured `artefact_data` endpoint supports `PUT`.
- **Delete** removes the model and its model-scoped annotations from the current scene state. Undo can restore it before history is lost; deleted records are omitted from exported JSON.

## Artefact details

The **Artefact** section is read-only and displays the model title, model URL (`gltf_file`), description, owner, keywords, and copyright. Additional source fields may be retained in JSON even though they are not displayed.

## Transforms

Open **Transforms** to move or rotate the model with numeric controls or the 3D gizmo. Translation is stored in model-local scene units and rotation in radians. Scale is fixed at `1, 1, 1` and is not part of THOTH's canonical scene format.

Measurements and semantic annotation points are stored in model-local coordinates, so they remain attached when the model is translated or rotated.
