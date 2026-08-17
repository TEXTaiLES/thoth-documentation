# HTTP API

THOTH's browser client uses a deployment-aware HTTP adapter. Local mode falls back to ATON APIs. HESTIA mode enables named endpoints under an authenticated same-origin `/hestia` gateway. Exact geodesic calculation is provided under `/api/v2/geodesic` whenever the THOTH gateway hook and native addon are installed; both Docker modes install them automatically.

This reference describes the interface used by the current client. It does not replace the [ATON REST API v2 documentation](https://aton.ispc.cnr.it/apiv2-docs/) or the upstream HESTIA API contract.

## Browser client behavior

Configured requests include cookies and use JSON unless a `FormData` body is supplied. The client normalizes results to:

```ts
type ClientResult =
  | { ok: true, data: unknown, status: number }
  | { ok: false, error: string, code?: string, status?: number }
```

This wrapper exists only in browser JavaScript; HTTP responses are not wrapped in it. HESTIA asset URLs that point to the configured public API origin are rewritten to same-origin `/hestia/...` URLs.

## Local ATON mode

With `use_endpoints: false`, THOTH uses these fallbacks:

| Operation | Interface |
| --- | --- |
| Load scene | `GET ../../api/v2/scenes/<scene-id>` through `ATON.SceneHub`. |
| Save scene | Two `PATCH` requests to the scene URL: `DEL` for the managed roots, then `ADD` with canonical content. |
| List models | ATON item listing at `items/<username>/models/`. |
| Load model | The selected ATON model path. |
| Artefact data | Reconstructed from the model already in `SceneStore`. |
| Metadata | Read and written in the local scene state. |
| RGB, multispectral, and sensor lists | Successful empty results because ATON has no equivalent endpoint. |

ATON authentication and authorization apply to these routes. THOTH's UI requires authentication before it invokes mutating actions.

## HESTIA gateway

All `/hestia` requests require a valid EGI session or HESTIA Portal/Directus session. The server rejects paths outside its allow-list, injects `Authorization: Bearer <HESTIA_API_AUTH_KEY>` upstream, and never exposes that key to the browser.

The default `hestia.json` maps these client operations:

| Client operation | Method and same-origin path | Parameters or body used by THOTH |
| --- | --- | --- |
| Load scene | `GET /hestia/scenes` | Query `scene_id`. Accepted response content is `content`, `scene.content`, `scenes[0].content`, or the response object itself; a string is JSON-decoded. |
| Create scene | `POST /hestia/scenes` | Supported by configuration; callers supply `scene_id`, `collaborative`, and `models`. The current UI does not create scenes. |
| Save scene | `PUT /hestia/scenes` | `{ "scene_id": "...", "body": { "content": <Scene> } }`. |
| List models | `GET /hestia/artifacts` | No parameters. Response may be an array, `artifacts`, or `data`. |
| Get model resource | `GET /hestia/artifacts/<artifact-id>` | Path ID. THOTH resolves `gltf_file`, `glb_file`, `public_url`, `url`, `path`, or `src`. |
| Get artefact data | `GET /hestia/artefacts/<artifact-id>` | Path ID. Normalized into artefact, annotations, sensor readings, and metadata. |
| List RGB images | `GET /hestia/rgb/images` | No parameters. |
| Get RGB image | `GET /hestia/rgb/image` | Query `image_name`. |
| List multispectral images | `GET /hestia/multispectral/images` | No parameters. |
| Get multispectral image | `GET /hestia/multispectral/image` | Query `image_name`. |
| List or get sensors | `GET /hestia/sensor-readings` | A single lookup uses `sensor_id` and `per_page=1`. |
| ECHOES scene | `PUT /hestia/echoes/<artefact-id>` | Canonical scene payload plus `scene_id` and `artefact_id`. `GET`, `POST`, and `PUT` are gateway-allowed for registered integrations. |

The gateway additionally permits authenticated `GET` requests for `/hestia/storage/<id>/...` and `/hestia/multispectral/file...` assets. `artefact_data` is read-only in the default HESTIA configuration; model data and metadata are persisted through scene export.

Disallowed gateway paths return:

```json
{
  "error": "HESTIA route is not enabled for THOTH",
  "code": "ROUTE_NOT_ALLOWED"
}
```

An unavailable upstream returns HTTP `503` with `UPSTREAM_TIMEOUT` or `UPSTREAM_UNAVAILABLE`.

## Authentication routes

These routes exist only when `THOTH_DEPLOYMENT_MODE=hestia`:

| Method and path | Purpose |
| --- | --- |
| `GET /a/thoth/egi-login?redirect=<local-path>` | Starts EGI OpenID Connect with PKCE. Only a local redirect path is accepted. |
| `GET /a/thoth/egi-callback` | Validates state, exchanges the code, resolves user info, sets the HTTP-only session, and returns to the saved path. |
| `GET /a/thoth/hestia-login?redirect=<local-path>` | Redirects through `/archive/user/login` on the configured Portal. |
| `GET /a/thoth/whoami` | Returns the normalized identity, `401` when unauthenticated, or `503` when the identity service is unavailable. |
| `POST /a/thoth/logout` | Clears THOTH and shared Directus session cookies. |
| `GET /a/thoth/egi-logout` | Compatibility alias for logout. |

The authenticated identity has `authenticated: true`, a provider, and normalized ID/username fields when supplied by the identity provider.

## Exact-geodesic routes

The gateway extension registers these same-origin routes independently of local or HESTIA mode. They are not protected by THOTH's HESTIA session middleware, so deployments that expose the ATON origin must apply any required access policy at the reverse proxy or network boundary.

### Load a mesh

```http
POST /api/v2/geodesic/load
Content-Type: application/json
```

```json
{
  "mesh_id": "textile_01:mesh_0",
  "vertices": [0, 0, 0, 1, 0, 0, 0, 1, 0],
  "faces": [0, 1, 2]
}
```

`vertices` is a flat array of finite XYZ values. `faces` is a flat triangle-index array of non-negative safe integers. Success returns:

```json
{ "status": true, "mesh_id": "textile_01:mesh_0" }
```

Validation returns HTTP `400` / `INVALID_GEODESIC_MESH`; a rejected mesh returns `422` / `GEODESIC_MESH_REJECTED`; and an unavailable native addon returns `503` / `GEODESIC_ADDON_UNAVAILABLE`.

### Query an exact path

```http
POST /api/v2/geodesic/exact
Content-Type: application/json
```

```json
{
  "mesh_id": "textile_01:mesh_0",
  "x1": 0,
  "y1": 0,
  "z1": 0,
  "x2": 1,
  "y2": 1,
  "z2": 0
}
```

Success returns `status: true`, a finite `distance`, and `path`, an array of `{ x, y, z }` model-local points. Errors include `INVALID_GEODESIC_QUERY` (`400`), `GEODESIC_MESH_NOT_FOUND` (`404`), `GEODESIC_PATH_NOT_FOUND` (`422`), and `GEODESIC_ADDON_UNAVAILABLE` (`503`).

Both geodesic client calls have a 120-second browser timeout. The server mesh cache is process-local and must be repopulated after restart.

## Endpoint configuration

The browser recognizes `scene`, `list_models`, `glb_model`, `artefact_data`, `list_schemas`, `schema`, `metadata`, `list_rgb_images`, `rgb_image`, `list_multispectral_images`, `multispectral_image`, `list_sensors`, `sensor`, `echoes`, and `authentication`. An endpoint object can specify:

```json
{
  "endpoint_url": "/example",
  "methods": ["GET", "PUT"],
  "item_path": true,
  "timeout_seconds": 30,
  "enabled": true
}
```

Except for the built-in geodesic routes, named endpoints are used only when `use_endpoints` is exactly `true`, `enabled` is `true`, and `endpoint_url` is present. See [Configuration reference](../deployment/configuration.md).
