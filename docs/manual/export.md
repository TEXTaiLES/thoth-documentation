# Export and download

THOTH keeps edits in browser memory until you explicitly persist or download them.

## Scene export

Select **Export changes** or press `Shift+E`, then choose:

- **Download scene** to save canonical scene JSON to your device; or
- **Export changes** to replace the stored scene content in the configured backend.

Opening this dialog requires authentication. Remote scene export is a full replacement, not an incremental history log. In local mode THOTH performs ATON scene patches that remove the managed roots and add the current canonical payload. In HESTIA mode it sends the payload to the configured scene `PUT` endpoint.

If THOTH was opened with `artefact_id`, a successful scene export also sends the scene to that artefact's ECHOES endpoint in HESTIA mode.

## Model and metadata downloads

The download action on a model row produces an artefact-data JSON document containing the model's artefact fields, annotations, first sensor record, and metadata. A remote model-data export button appears only when the backend supports `PUT` for `artefact_data`.

The metadata editor can download only the selected model's canonical metadata. A separate remote metadata export appears only when supported by the active configuration.

## Export filtering

Scene export includes `models` and the top-level `collaborative` flag. Runtime objects, helper nodes, cached paths, materials, and trashed records are omitted. Review the [Scene JSON reference](../scene/scene_structure.md) before integrating downloaded files with another system.
