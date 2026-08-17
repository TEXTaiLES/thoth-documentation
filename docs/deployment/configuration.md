# Configuration reference

THOTH separates browser-visible deployment configuration from server-only secrets.

## Browser configuration selection

The browser first requests:

```text
/a/thoth/config/deployment.json
```

It validates the selector's `mode` and `source`, loads the named JSON file, then merges any non-secret `runtime` values supplied by the selector.

The repository contains:

- `config/deployment.json`: committed selector, defaulting to `local.json`;
- `config/local.json`: ATON authentication, ATON fallbacks, and no external endpoints; and
- `config/hestia.json`: HESTIA authentication plus named same-origin endpoints.

In HESTIA Docker mode, the gateway intercepts the selector request and returns:

```json
{
  "mode": "hestia",
  "source": "hestia.json",
  "runtime": {
    "hestiaApiPublicUrl": "https://api.example.org",
    "auth": { "portalUrl": "https://portal.example.org" }
  }
}
```

Secrets are never included in this response.

## Main browser fields

| Field | Meaning |
| --- | --- |
| `deploymentMode` | `local` or `hestia`. |
| `auth.mode` | `aton` or `hestia`. |
| `ATONSceneUrl` | Local ATON scene base URL used by the fallback loader/exporter. |
| `ATONModelUrl` | Local ATON model base URL retained for integration configuration. |
| `schemaListUrl` | Schema-list resource. |
| `defaultSchemaName` | Schema used when metadata has no explicit schema. |
| `use_endpoints` | Must be `true` before non-built-in named endpoints are enabled. |
| `endpoints` | Named endpoint definitions used by the API client. |

An endpoint definition supports `endpoint_url`, allowed `methods`, `enabled`, optional `item_path`, and optional `timeout_seconds`. See [HTTP API](../api/rest.md#endpoint-configuration).

## HESTIA environment variables

| Variable | Default | Purpose |
| --- | --- | --- |
| `THOTH_DEPLOYMENT_MODE` | `local` | Enables HESTIA gateway and auth only when set to `hestia`. |
| `HESTIA_API_TARGET` | `http://api:5000` | Upstream HESTIA API on the Docker network. |
| `HESTIA_API_PUBLIC_URL` | `https://api.textailes.athenarc.gr` | Public origin found in returned asset URLs. |
| `HESTIA_DIRECTUS_TARGET` | `http://directus:8055` | Directus target used to refresh and end Portal sessions. |
| `HESTIA_PORTAL_URL` | `https://textailes.athenarc.gr` | Browser-visible Archive Portal origin. |
| `HESTIA_COOKIE_NAME` | `textailes_refresh_token` | Shared Directus refresh cookie. |
| `HESTIA_COOKIE_DOMAIN` | `.textailes.athenarc.gr` | Shared parent cookie domain. |
| `HESTIA_API_AUTH_KEY` | none | Server-side HESTIA bearer credential. Required. |
| `EGI_AUTHORIZE_URL` | none | EGI authorization endpoint. Required. |
| `EGI_TOKEN_URL` | none | EGI token endpoint. Required. |
| `EGI_USERINFO_URL` | none | EGI user-info endpoint. Required. |
| `EGI_CLIENT_ID` | none | OAuth client ID. Required. |
| `EGI_CLIENT_SECRET` | none | OAuth client secret. Required. |
| `EGI_REDIRECT_URI` | deployment-specific | Registered callback URL. Required. |
| `THOTH_SESSION_SECRET` | none | HMAC secret for THOTH sessions. Required. |
| `THOTH_HOST_DOMAIN` | `thoth.textailes.athenarc.gr` | Traefik host rule. |
| `THOTH_GEODESIC_ADDON_PATH` | built-in path search | Optional explicit native addon location. |

`server/deployment/gateway-extension.js` validates every required HESTIA variable during startup. Use long, random values for session and service secrets and rotate them according to the deployment's security policy.

## ATON source boundary

Local execution does not load HESTIA routes. Docker installs one gateway loader line into the image's private ATON copy; it does not edit or mount a host ATON source file. Native Exact Geodesic is the exception: its documented installer explicitly updates the selected host `services/ATON.service.main.js`, and only after an administrator runs it.
