# Metadata

Metadata describes a model and is separate from annotation details. Open a model's **Metadata** row in Scene Structure to edit it.

## Schema-driven editor

Each metadata record has a schema descriptor and an `attributes` object. THOTH loads the configured schema list, validates supported field types, and builds an editor from the selected schema. The bundled fallback is `puc_schema`.

Supported schema field types are:

- `string`, `text`, `url`, `date`, and `reference`;
- `integer` and `float`;
- `bool` or `boolean`;
- `enum`, `enum-multiple`, or `multienum`; and
- nested `group` fields.

Changing the selected schema creates a fresh attribute set with that schema's defaults; it does not map values from the previous schema automatically.

## Save, export, and download

- **Save changes** updates the current scene state and history.
- **Download metadata** saves the canonical metadata object as local JSON.
- **Export metadata** appears when the active backend provides a metadata `PUT` operation. In HESTIA mode metadata is normally persisted as part of full scene export, so no separate export button is shown.

Canonical metadata has the shape `{ "schema": { ... }, "attributes": { ... } }`. See [Scene JSON](../scene/scene_structure.md#metadata).
