# Scene JSON

This page defines the canonical scene content parsed and exported by the current THOTH `dev` branch. It is intended for backend and integration developers.

The examples use TypeScript-like notation: `Record<string, T>` is an object keyed by ID, `T[]` is an array, and `?` marks an optional field. Coordinates are model-local and rotations are in radians.

## Scene

```ts
type Scene = {
  models: Record<string, Model>,
  collaborative: boolean
}
```

`models` is an object, not an array. The minimal scene is:

```json
{
  "models": {},
  "collaborative": false
}
```

## Model

```ts
type Model = {
  id: string,
  artefact: Artefact,
  metadata: Metadata,
  transforms: Transforms,
  annotations: Annotations,
  sensors: Sensor[]
}
```

The key in `Scene.models` and the model's `id` should be identical. Runtime-only fields and models marked `trash: true` are omitted from export.

### Artefact

```ts
type Artefact = {
  title: string,
  gltf_file: string,
  description: string,
  owner: string,
  keywords: string[],
  copyright: string,
  [additionalField: string]: unknown
}
```

`gltf_file` is the canonical resource URL even when it points to a `.glb`. The parser also recognizes `glb_file`, `url`, `path`, or `src` as URL aliases and `name` as a title alias. Additional source fields may be retained.

### Metadata

```ts
type Metadata = {
  schema: {
    name: string,
    version: string | number,
    description: string,
    url: string
  },
  attributes: Record<string, unknown>
}
```

Legacy metadata may be a flat attributes object with an optional `schemaName`. THOTH normalizes it to `{ schema, attributes }`. A non-empty flat object without a schema name defaults to `puc_schema`.

### Transforms

```ts
type Vector3 = { x: number, y: number, z: number }

type Transforms = {
  translation: Vector3,
  rotation: Vector3
}
```

Both vectors default to zero. Input vectors may also be `[x, y, z]`; `position` is accepted as an alias for `translation`, and the legacy singular `transform` container is accepted. Scale is not canonical and THOTH forces loaded model scale to `{ x: 1, y: 1, z: 1 }`.

### Sensors

THOTH preserves `sensors` as an opaque array and does not validate or edit each entry. Model-data export uses the first entry. A sensor ID may be a primitive or an object field named `related_sensor_id`, `sensor_id`, or `id`; `latest_reading` defaults to `{}`.

## Annotations

```ts
type Annotations = {
  selections: Record<string, Selection>,
  measurements: Record<string, Measurement>,
  semantic_annotations: Record<string, SemanticAnnotation>
}
```

All three collections are objects keyed by annotation ID. For legacy input, THOTH also accepts these collections directly on the model, but canonical export nests them under `annotations`.

### Shared annotation fields

```ts
type BaseAnnotation = {
  id: string,
  name: string,
  description: string,
  related_rgb_images: Relation[],
  related_multispectral_images: Relation[],
  related_artefacts: Relation[],
  annotation: Record<string, unknown>,
  visible: boolean
}

type Relation = {
  id: string,
  name: string,
  url: string,
  [additionalField: string]: unknown
}
```

Relations retain additional fields. A multispectral relation can therefore include `urls: Record<string, string>`. The parser derives missing canonical fields from `title`, `image_name`, `image_url`, `gltf_file`, `path`, and `src`. A primitive relation value becomes both its `id` and `name`. Visibility defaults to `true`.

### Selection

```ts
type Selection = BaseAnnotation & {
  annotation: {
    selected_faces: Record<string, string>,
    selection_color: string
  }
}
```

`selected_faces` is keyed by mesh ID. Each exported value is a comma-separated list with inclusive ranges:

```json
{
  "mesh_0": "1-3,8,11-14",
  "mesh_1": "4,9"
}
```

The parser also accepts arrays or other iterables of non-negative integer face IDs. Legacy top-level `selected_faces`, `selection`, `selection_color`, and `highlightColor` fields are accepted. Canonical export nests the data in `annotation`. The color is a hexadecimal string such as `#ff8800`.

### Measurement

```ts
type Measurement = BaseAnnotation & {
  annotation: {
    coordinate_space: "model_local",
    distance: number,
    distance_type: "euclidean" | "geodesic" | "geodesicExact" | string,
    point1: ScenePoint,
    point2: ScenePoint
  }
}
```

`distance_type` defaults to `euclidean`. Canonical export always writes `coordinate_space: "model_local"`. Runtime and legacy input may use top-level `distance`, `distance_type`, `distanceType`, `point1`, `point2`, or `points`. A legacy nested point without the coordinate-space marker is interpreted as world-space and converted while the model is available.

The rendered geodesic `path` is runtime-only and is not exported.

### Semantic annotation

```ts
type SemanticAnnotation = BaseAnnotation & {
  annotation: {
    coordinate_space: "model_local",
    point: ScenePoint
  }
}
```

Canonical export always writes `coordinate_space: "model_local"`. A legacy top-level `point`, or a nested point without the coordinate-space marker, is accepted and normalized.

### Scene point

```ts
type ScenePoint = {
  x: number,
  y: number,
  z: number,
  face_id: number | null
}
```

The parser also recognizes `faceId`, `meshId`/`mesh_id`, `meshName`/`mesh_name`, and a nested `coords` vector. Mesh identifiers help runtime geometry resolution but are not part of canonical point export.

## Complete example

```json
{
  "models": {
    "textile_01": {
      "id": "textile_01",
      "artefact": {
        "title": "Textile 01",
        "gltf_file": "models/textile_01.glb",
        "description": "",
        "owner": "",
        "keywords": [],
        "copyright": ""
      },
      "metadata": {
        "schema": {
          "name": "puc_schema",
          "version": 1,
          "description": "Dedicated TEXTaiLES schema",
          "url": ""
        },
        "attributes": {}
      },
      "transforms": {
        "translation": { "x": 0, "y": 0, "z": 0 },
        "rotation": { "x": 0, "y": 0, "z": 0 }
      },
      "annotations": {
        "selections": {
          "selection_1": {
            "id": "selection_1",
            "name": "Example selection",
            "description": "Woven border",
            "related_rgb_images": [],
            "related_multispectral_images": [],
            "related_artefacts": [],
            "annotation": {
              "selected_faces": { "mesh_0": "1-3,8" },
              "selection_color": "#ff8800"
            },
            "visible": true
          }
        },
        "measurements": {},
        "semantic_annotations": {}
      },
      "sensors": []
    }
  },
  "collaborative": false
}
```

## Export rules

- Missing model fields are normalized to empty canonical values.
- Trashed models and annotations are excluded.
- Runtime state such as Three.js nodes, materials, mesh references, cached paths, `model_id`, and helper coordinates is excluded.
- Additional artefact, relation, annotation-payload, and sensor fields can survive normalization. Consumers should tolerate unknown fields.
