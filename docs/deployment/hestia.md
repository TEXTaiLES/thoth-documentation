# HESTIA integration

HESTIA mode connects THOTH to an independently deployed HESTIA/Directus stack. Browser requests stay on the THOTH origin: an authenticated gateway validates the requested path, injects the server-side service credential, and proxies it to HESTIA.

## Prerequisites

- The base THOTH Compose files and Docker requirements.
- A running HESTIA/Directus Compose project.
- An external Docker network named `textailes` shared with that project.
- An EGI OpenID Connect client registration.
- HTTPS and compatible cookie domains in production.

Confirm the shared network exists:

```sh
docker network inspect textailes
```

Start HESTIA first, or create the network explicitly if your deployment process does not create it.

## Configure

Copy `.env.example` to `.env` and fill every blank value. The required secrets are:

- `HESTIA_API_AUTH_KEY`;
- `THOTH_SESSION_SECRET`;
- `EGI_CLIENT_ID`; and
- `EGI_CLIENT_SECRET`.

The required EGI endpoints are `EGI_AUTHORIZE_URL`, `EGI_TOKEN_URL`, and `EGI_USERINFO_URL`. `EGI_REDIRECT_URI` must match the client registration byte-for-byte. For the default production host it is:

```text
https://thoth.textailes.athenarc.gr/a/thoth/egi-callback
```

Do not put service keys or OAuth client secrets in `config/*.json`; those files are served to browsers.

## Start

```sh
docker compose -f docker-compose.yml -f docker-compose.hestia.yml up --build -d
```

For live-mounted development:

```sh
docker compose -f docker-compose.yml -f docker-compose.hestia.yml -f docker-compose.dev.yml up --build
```

The HESTIA override sets `THOTH_DEPLOYMENT_MODE=hestia`, joins the external network, and adds the default Traefik HTTPS route. At runtime, the gateway serves a HESTIA deployment selector before ATON's static-file handler can serve the committed local selector.

## Authentication flows

- **Login with EGI** uses THOTH's Authorization Code flow with PKCE and accepts an identity confirmed by EGI.
- **Login through HESTIA Portal** redirects through `/archive/user/login`, allowing HESTIA to apply Directus registration and account-status rules.

Either session authorizes the allow-listed `/hestia` proxy. Signing out a Portal session also clears the shared Directus refresh cookie.

## Validate

Resolve variables and merged Compose settings before deployment:

```sh
docker compose -f docker-compose.yml -f docker-compose.hestia.yml config
```

Then verify sign-in, scene load and export, one proxied model, and any RGB or multispectral assets from the public API origin.

## Troubleshooting

- **Compose reports a missing variable:** every required secret and EGI setting must be non-empty; startup intentionally fails otherwise.
- **External network not found:** start HESTIA first or create the `textailes` network.
- **EGI callback fails:** compare the registered redirect URI, public HTTPS host, client credentials, and configured EGI URLs.
- **Portal returns to the archive:** ensure the THOTH and HESTIA hosts share `HESTIA_COOKIE_DOMAIN` and use HTTPS.
- **Authentication service unavailable:** confirm Directus is reachable at `HESTIA_DIRECTUS_TARGET` from the shared network.
- **Model or image returns 401:** complete one login flow and confirm `HESTIA_API_PUBLIC_URL` matches the origin used in returned asset URLs.

See [Configuration reference](configuration.md) for every environment variable and [HTTP API](../api/rest.md) for the proxy allow-list.
