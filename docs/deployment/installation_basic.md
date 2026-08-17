# Native ATON deployment

Run THOTH as an ATON web app when you already operate ATON or need direct access to its configuration and storage.

## Requirements

- Node.js and npm supported by the target ATON checkout;
- Git; and
- for Exact Geodesic: Python, a C++17 compiler, and the platform build tools required by `node-gyp`.

The THOTH Docker image is tested against ATON commit `22afaf28bcb6deb57ff1ea8e3737336a5a85d076`. Use that revision when you need parity with the packaged deployment, or validate your chosen ATON revision before release.

## Install

Clone ATON, then place THOTH exactly at `wapps/thoth`:

```sh
git clone https://github.com/phoenixbf/aton.git
cd aton
git switch --detach 22afaf28bcb6deb57ff1ea8e3737336a5a85d076
git clone https://github.com/TEXTaiLES/thoth.git wapps/thoth
npm install
```

Local mode is already selected by `wapps/thoth/config/deployment.json`; it needs no THOTH `.env` file. Configure ATON users and storage through ATON's normal configuration.

## Enable Exact Geodesic

Build the native addon:

```sh
cd wapps/thoth/geodesic/geodesic_addon
npm ci
cd ../../../..
```

Then install THOTH's gateway loader into this ATON checkout:

```sh
node wapps/thoth/server/deployment/install-gateway.cjs services/ATON.service.main.js
```

The installer requires the explicit target path and adds one idempotent loader line. It is not run by `npm start`. This is the only native step that edits an ATON source file, and it is needed only for THOTH's `/api/v2/geodesic/*` routes. If you do not need Exact Geodesic, omit the addon build and loader installation.

## Run

From the ATON root:

```sh
npm start
```

Open a known scene:

```text
http://localhost:8080/a/thoth/?scene_id=<scene-id>
```

The ATON landing page at `http://localhost:8080/` confirms the service is reachable, but it does not select a THOTH scene.

## Update

Update ATON and THOTH as separate repositories. After updating THOTH, rerun `npm ci` in the geodesic addon when its package or native source changed, and rerun the gateway installer. The installer is safe to repeat and does not add duplicate loader lines.
