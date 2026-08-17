# Internal events and operations

THOTH uses ATON's local `EventHub` for input and application commands and ATON Photon for collaboration. The stable synchronization unit is a scene operation; UI events translate user actions into those operations.

## Local event API

After `THOTH.Events.setup()`, the application exposes:

```js
THOTH.on("eventName", data => {
  // Handle a local event.
});

THOTH.fire("eventName", data, immediate);
```

`THOTH.fire` wraps `ATON.fire` with an authentication check for mutating commands. It returns `false` and opens the sign-in UI when a protected event is fired without an authenticated user.

### Model and selection commands

| Event | Payload | Effect |
| --- | --- | --- |
| `addModel` | model ID string | Resolves model and artefact data, then creates a model operation. |
| `deleteModel` | model ID string | Marks the model for deletion. |
| `modelTransformPos` | `{ modelName, value: { x, y, z } }` | Updates translation. |
| `modelTransformRot` | `{ modelName, value: { x, y, z } }` | Updates rotation. |
| `createSelection` | optional `{ modelId }` | Creates and activates a selection. |
| `deleteSelection` | selection ID | Deletes the selection resolved by ID. |
| `editSelectionMetadata` | `{ id, data, annotationData, prevData? }` | Updates the selection's shared annotation fields and legacy `metadata` value. |
| `renameSelection` | `{ id, data, prevData? }` | Updates the name. |

Selection IDs are model-scoped in storage. Code that can access more than one model should prefer `THOTH.Annotations` or operations with an explicit `target.model_id` instead of relying on ID-only UI commands.

### Measurement and semantic-annotation commands

| Event | Payload | Effect |
| --- | --- | --- |
| `selectMeasure` | none | Activates Measure. |
| `addMeasurementPoint` | none | Adds the current surface hit. |
| `createMeasurement` | none for the interactive flow, or `{ id, data }` | Computes or commits a measurement. |
| `deleteMeasurement` | `{ id, point1?, point2? }` | Deletes a measurement. |
| `renameMeasurement` | `{ id, value }` | Updates its name. |
| `editMeasurement` | `{ id, data, prevData? }` | Updates shared fields. |
| `toggleMeasurementVisibility` | measurement ID | Toggles visibility. |
| `selectSemanticAnnotation` | none | Activates the point annotation tool. |
| `addSemanticAnnotationPoint` | none | Starts details for the current surface hit. |
| `createSemanticAnnotation` | `{ id, data }` | Creates the annotation. |
| `updateSemanticAnnotation` | `{ id, data, prevData? }` | Updates it. |
| `deleteSemanticAnnotation` | annotation ID | Deletes it. |
| `toggleSemanticAnnotationVisibility` | annotation ID | Toggles visibility. |

### Tool and input events

`selectBrush`, `selectEraser`, `selectLasso`, and `selectNone` change the active tool. `useBrush`, `endBrush`, `useEraser`, `endEraser`, `startLasso`, `updateLasso`, `endLassoAdd`, `endLassoDel`, and `endAllToolOps` drive interactive selection edits.

THOTH also emits `MouseLeftDown`, `MouseLeftUp`, `MouseRightDown`, `MouseRightUp`, `MouseMove`, `KeyDown`, and `KeyUp` from browser input. Keyboard payloads use `KeyboardEvent.code`, for example `KeyB` or `Digit1`.

## Operation API

Use operations for code that must integrate with undo/redo or collaboration:

```js
const operation = THOTH.Ops.makeOperation(
  "model.update_transform",
  { model_id: "textile_01", field: "translation" },
  {
    translation: { x: 1, y: 0, z: 0 },
    rotation: { x: 0, y: 0, z: 0 }
  },
  previousTransforms
);

THOTH.Ops.applyLocal(operation);
```

The wire shape is:

```ts
type Operation = {
  type: string,
  target: {
    model_id?: string,
    collection?: "selections" | "measurements" | "semantic_annotations",
    item_id?: string | number,
    field?: string
  },
  value: unknown,
  prev_value: unknown,
  user_id?: string,
  timestamp?: number,
  source?: "local" | "remote" | "history"
}
```

Supported types are:

- `model.create`, `model.delete`, `model.update_artefact`, `model.update_metadata`, and `model.update_transform`;
- `selection.create`, `selection.update`, and `selection.delete`;
- `measurement.create`, `measurement.update`, and `measurement.delete`; and
- `semantic_annotation.create`, `semantic_annotation.update`, and `semantic_annotation.delete`.

`applyLocal` adds `user_id`, a millisecond timestamp, and `source: "local"`; applies the operation; pushes its inverse to history; and broadcasts it when collaboration is enabled. `applyRemote` ignores messages from the local user and does not modify local history. `THOTH.Ops.invert(operation)` exchanges new and previous values and swaps `.create` with `.delete` where needed.

Conflict filtering is last-timestamp-wins per model field or collection item. Timestamps are supplied by browser clocks, so deployments that require stronger ordering should add an authoritative backend protocol.

## Photon messages

```js
THOTH.onPhoton("thoth.operation", operation => {
  // THOTH normally applies this automatically.
});

THOTH.firePhoton("thoth.operation", operation);
```

| Message | Payload | Purpose |
| --- | --- | --- |
| `thoth.operation` | `Operation` | Broadcast one canonical mutation. |
| `syncScene` | canonical scene object | Replace runtime scene state when a participant joins. |

On `VRC_UserEnter`, a connected participant emits `syncScene` with `THOTH.getExportData()`. Consumers should treat both Photon message names as internal protocol and keep clients on compatible THOTH versions.
