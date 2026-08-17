# Open a scene

Open THOTH with a `scene_id` query parameter:

```text
<base-url>/a/thoth/?scene_id=<scene-id>
```

For a native ATON installation, the default base URL is `http://localhost:8080`. For the default local Docker deployment it is `http://localhost:8054`.

For example:

```text
http://localhost:8080/a/thoth/?scene_id=samples/venus
```

Scene identifiers may contain an owner path, such as `samples/venus`. Encode reserved URL characters if you construct this address programmatically.

The optional `artefact_id` parameter links scene export to an existing ECHOES Digital Twin in HESTIA mode:

```text
https://thoth.example.org/a/thoth/?scene_id=<scene-id>&artefact_id=<artefact-id>
```

When `artefact_id` is present, a successful scene export is followed by a `PUT` to the configured ECHOES endpoint. The ECHOES artefact must already be registered.

## Access behavior

- In local ATON mode, an unauthenticated user may view a scene, but editing and export actions require sign-in.
- In HESTIA mode, sign-in is required before the scene can be loaded.
- If `scene_id` is omitted, THOTH opens without loading a scene.
- If the scene cannot be found or its content is not valid JSON, THOTH reports the error and does not parse it.

Scenes can be created through the backend before opening them in THOTH. See the [HTTP API](../api/rest.md) and [scene JSON reference](scene_structure.md) for the expected interface and content.
