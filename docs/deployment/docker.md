# Docker deployment

The default Compose project builds a self-contained local ATON + THOTH image. It installs ATON at the revision pinned in the Dockerfile, builds the native Exact Geodesic addon, and installs the THOTH gateway hook into the image's private ATON copy.

## Requirements

- Docker Engine or Docker Desktop; and
- the Docker Compose plugin (`docker compose`).

## Start local mode

From the THOTH repository root:

```sh
docker compose up --build -d
```

Open:

```text
http://localhost:8054/a/thoth/?scene_id=<scene-id>
```

The host port is `8054`; ATON listens on `8080` inside the container. Local Docker needs no `.env` file.

## Persistence

Compose creates two named volumes:

- `aton-data` for scene and model data; and
- `aton-config` for ATON configuration and users.

Container recreation preserves these volumes. Removing the volumes removes the persisted local state, so back them up before destructive Compose operations.

## Development bind mount

To mount the working tree over the image's THOTH copy:

```sh
docker compose -f docker-compose.yml -f docker-compose.dev.yml up --build
```

The compiled geodesic addon is copied outside `/aton/wapps/thoth` during the image build, so the bind mount does not hide it. Use this override for source iteration, not for verifying the exact files baked into a release image.

## Validate and inspect

```sh
docker compose config
docker compose ps
docker compose logs -f thoth
```

For a HESTIA-connected deployment, continue with [HESTIA integration](hestia.md); it adds an override rather than replacing the base Compose file.
