# Measurements

Use the Measure tool (`M`) to select two points on the same model. After THOTH computes the distance, complete the annotation details dialog and choose **Create measurement**. The scene tree then provides edit, visibility, and delete controls.

Distances are displayed to four decimal places. THOTH uses the scene's model units; deployments should ensure imported models use a consistent real-world scale if values are expected to represent metres.

## Distance modes

| Mode | Calculation | Requirements |
| --- | --- | --- |
| Euclidean | Straight line between the two selected points. | The points must belong to the same model. |
| Geodesic | Approximate shortest path along connected mesh vertices using A*. | Both points must be on the same mesh and a connected vertex path must exist. |
| Exact Geodesic | Surface path computed by the native Kirsanov/Mitchell-Mount-Papadimitriou addon. | Both points must be on the same triangular, manifold mesh and the server addon must be installed. |

The approximate mode snaps the endpoints to nearby vertices. Exact mode welds coincident vertices and removes invalid or duplicate triangles before sending a model-local mesh to the same-origin server. It rejects non-manifold edges. The server caches the mesh by ID for later exact queries, but the cache is in memory and may be empty after a restart.

THOTH reports an error instead of creating a measurement when the points span models or meshes, no path exists, the geometry is incompatible, or the exact-geodesic service is unavailable.

All canonical measurement endpoints are stored in model-local coordinates. The computed display path is runtime data; export stores the endpoints, distance, and distance type.
