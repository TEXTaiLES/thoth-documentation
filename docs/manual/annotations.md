# Annotation details

Selections, measurements, and semantic annotations share the same descriptive fields. Open an annotation's details action in the scene tree to edit them.

## Shared fields

- **Name**: the label shown in the scene tree and annotation UI.
- **Description**: free text describing the observation.
- **Related RGB images**: references with an ID, name, and image URL.
- **Related multispectral images**: references that may include a wavelength-to-URL map.
- **Related artefacts**: references to other 3D artefacts.
- **Visibility**: whether the selection highlight, measurement graphics, or semantic marker is displayed.

The relation pickers query the active backend. HESTIA mode can supply remote artefacts and image records. Local ATON mode has no RGB, multispectral, or sensor equivalent and therefore shows empty image lists; model relations can still use the local model list.

RGB relations can be previewed and downloaded when a URL is available. Multispectral relations can be previewed wavelength by wavelength; direct multispectral download is not enabled in the current UI.

Saving the details dialog updates the in-memory scene, adds the operation to local history, and broadcasts it when the scene is collaborative. It does not persist the scene to the backend until you [export changes](export.md).
