# Sign in

Use the user button in the upper-right corner to sign in or out. Authentication controls whether THOTH permits editing; it does not change the scene JSON format.

## Local ATON mode

The sign-in dialog uses ATON username/password authentication. User accounts and credentials are managed in the host ATON installation, normally through `config/users.json` or the deployment's mounted ATON configuration.

THOTH does not create a default administrator and this documentation does not assume shared demo credentials. Ask the instance administrator for an account.

Unauthenticated users can load and inspect local scenes. THOTH requests sign-in when they try to import or delete models, edit transforms or metadata, create or edit annotations, use undo/redo, or export changes.

## HESTIA mode

HESTIA mode requires a session before it loads a scene. The dialog offers two independent flows:

- **Login with EGI** starts THOTH's OpenID Connect Authorization Code flow with PKCE.
- **Login through HESTIA Portal** sends the browser through the Archive Portal, which applies HESTIA's Directus registration and account-status rules.

Both flows return to the same THOTH URL, preserving `scene_id`, `artefact_id`, and other query parameters. THOTH sessions are stored in HTTP-only cookies; service credentials are never sent to browser code.

Signing out of a HESTIA Portal session also clears the shared Directus refresh cookie, so the browser is signed out of the Archive Portal as well.
