# Collaborative scenes

THOTH can synchronize edits between users viewing the same ATON Photon room. Collaboration is enabled by the top-level scene property:

```json
{
  "models": {},
  "collaborative": true
}
```

Photon connects after an authenticated user loads the scene. When a user enters the room, an existing participant sends the current exported scene state. Subsequent model, transform, metadata, selection, measurement, and semantic-annotation changes are sent as `thoth.operation` messages.

Each operation contains its target, new and previous values, user ID, and timestamp. Remote changes are applied without entering the recipient's undo history. If two operations affect the same target, THOTH ignores one whose timestamp is older than the latest operation already applied to that target.

## What users should expect

- Changes appear in other connected clients without a page reload.
- Undo and redo affect only operations in the current user's local history, then broadcast the resulting inverse operation.
- Export still writes the complete current scene to the configured backend. Real-time synchronization does not replace persistence.
- A scene with `"collaborative": false`, or without the field, behaves as a single-user scene and does not broadcast THOTH operations.

The collaboration flag belongs at the scene root. It is not a model or annotation property. See [Internal events and operations](../api/events.md) for the wire format.
