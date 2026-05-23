# Complete Example: auth.md

A working example of an `auth.md` file for the fictional "Acme Notes" service that supports all three registration methods.

---

## Example Context

- **Service:** Acme Notes (collaborative notes app)
- **Resource Server:** `https://api.acmenotes.com`
- **Auth Server:** `https://auth.acmenotes.com`
- **Supported flows:** Agent Verified (ID-JAG), User Claimed Anonymous, User Claimed Email
- **Scopes:** `notes.read`, `notes.write`, `notes.admin`
- **Credentials:** `access_token` for ID-JAG, `api_key` for anonymous and email

---

## The File

```markdown
# auth.md

You are an agent. This service supports **agentic registration**: discover → register → (claim if needed) → call API → handle revocation. Follow the steps in order; do not skip ahead.

The resource server is `https://api.acmenotes.com` and the authorization server is `https://auth.acmenotes.com`.

## Step 1 — Discover

Discovery is two hops. The 401 response that pointed you here carries a `WWW-Authenticate` header with the PRM URL:

\```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer resource_metadata="https://api.acmenotes.com/.well-known/oauth-protected-resource"
\```

### 1a. Fetch the Protected Resource Metadata

\```http
GET /.well-known/oauth-protected-resource
Host: api.acmenotes.com
\```

Response:

\```json
{
  "resource": "https://api.acmenotes.com/",
  "resource_name": "Acme Notes",
  "resource_logo_uri": "https://acmenotes.com/logo.png",
  "authorization_servers": ["https://auth.acmenotes.com/"],
  "scopes_supported": ["notes.read", "notes.write", "notes.admin"],
  "bearer_methods_supported": ["header"]
}
\```

### 1b. Fetch the Authorization Server metadata

\```http
GET /.well-known/oauth-authorization-server
Host: auth.acmenotes.com
\```

Response:

\```json
{
  "resource": "https://api.acmenotes.com/",
  "authorization_servers": ["https://auth.acmenotes.com/"],
  "scopes_supported": ["notes.read", "notes.write", "notes.admin"],
  "bearer_methods_supported": ["header"],
  "agent_auth": {
    "skill": "https://acmenotes.com/auth.md",
    "register_uri": "https://auth.acmenotes.com/agent/auth",
    "claim_uri": "https://auth.acmenotes.com/agent/auth/claim",
    "revocation_uri": "https://auth.acmenotes.com/agent/auth/revoke",
    "identity_types_supported": ["anonymous", "identity_assertion"],
    "anonymous": {
      "credential_types_supported": ["api_key"]
    },
    "identity_assertion": {
      "assertion_types_supported": [
        "urn:ietf:params:oauth:token-type:id-jag",
        "verified_email"
      ],
      "credential_types_supported": ["access_token", "api_key"]
    },
    "events_supported": [
      "https://schemas.workos.com/events/agent/auth/identity/assertion/revoked"
    ]
  }
}
\```

## Step 2 — Pick a method

1. **You have a session tied to a user identity and can exchange it for an ID-JAG** → identity_assertion + id-jag.
2. **You have only the user's email** → identity_assertion + email. Claim ceremony required.
3. **You have neither** → anonymous. Claim ceremony optional.

Cross-check against the `agent_auth` block before proceeding.

## Step 3 — Register

Before sending an `identity_assertion`, surface "Acme Notes" and its logo to the user and confirm consent. Skip for `anonymous`.

### identity_assertion + id-jag

Mint the ID-JAG with `aud: "https://auth.acmenotes.com"`.

\```http
POST /agent/auth
Host: auth.acmenotes.com
Content-Type: application/json

{
  "type": "identity_assertion",
  "assertion_type": "urn:ietf:params:oauth:token-type:id-jag",
  "assertion": "<ID-JAG>",
  "requested_credential_type": "access_token"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_01ABC123DEF456",
  "registration_type": "agent-provider",
  "credential_type": "access_token",
  "credential": "eyJhbGciOiJSUzI1NiJ9...",
  "credential_expires": "2026-05-22T14:00:00.000Z",
  "scopes": ["notes.read", "notes.write"]
}
\```

Go to Step 5.

### identity_assertion + email

\```http
POST /agent/auth
Host: auth.acmenotes.com
Content-Type: application/json

{
  "type": "identity_assertion",
  "assertion_type": "verified_email",
  "assertion": "jane@example.com",
  "requested_credential_type": "api_key"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_01DEF789GHI012",
  "registration_type": "email-verification",
  "claim_url": "https://auth.acmenotes.com/agent/auth/claim",
  "claim_token": "clm_xYz789AbC012dEf",
  "claim_token_expires": "2026-05-22T13:30:00.000Z",
  "post_claim_scopes": ["notes.read", "notes.write"]
}
\```

No credential yet. Go to Step 4.

### anonymous

\```http
POST /agent/auth
Host: auth.acmenotes.com
Content-Type: application/json

{
  "type": "anonymous",
  "requested_credential_type": "api_key"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_01GHI345JKL678",
  "registration_type": "anonymous",
  "credential_type": "api_key",
  "credential": "acme_key_test_EXAMPLE_00000000",
  "credential_expires": null,
  "scopes": ["notes.read"],
  "claim_url": "https://auth.acmenotes.com/agent/auth/claim",
  "claim_token": "clm_aBc123DeF456gHi",
  "claim_token_expires": "2026-05-22T13:30:00.000Z",
  "post_claim_scopes": ["notes.read", "notes.write"]
}
\```

Credential works immediately at pre-claim scopes. To upgrade, go to Step 4.

## Step 4 — Claim ceremony

### 4a. Trigger the claim email (anonymous only)

Skip for email registrations — email was sent in Step 3.

\```http
POST /agent/auth/claim
Host: auth.acmenotes.com
Content-Type: application/json

{
  "claim_token": "clm_aBc123DeF456gHi",
  "email": "jane@example.com"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_01GHI345JKL678",
  "claim_attempt_id": "att_001",
  "status": "initiated",
  "expires_at": "2026-05-22T13:40:00.000Z"
}
\```

### 4b. Wait for the user's OTP

Ask: "Check your email from Acme Notes and tell me the 6-digit code."

### 4c. Submit the OTP

\```http
POST /agent/auth/claim/complete
Host: auth.acmenotes.com
Content-Type: application/json

{
  "claim_token": "clm_aBc123DeF456gHi",
  "otp": "847291"
}
\```

Response (anonymous — scopes upgraded):

\```json
{
  "registration_id": "reg_01GHI345JKL678",
  "status": "claimed"
}
\```

Response (email-verification — credential issued):

\```json
{
  "registration_id": "reg_01DEF789GHI012",
  "status": "claimed",
  "credential_type": "api_key",
  "credential": "acme_key_live_EXAMPLE_11111111",
  "credential_expires": null,
  "scopes": ["notes.read", "notes.write"]
}
\```

## Step 5 — Use the credential

\```http
GET /api/notes
Host: api.acmenotes.com
Authorization: Bearer acme_key_live_EXAMPLE_11111111
\```

- `access_token`: re-register with fresh ID-JAG on expiry.
- `api_key`: no expiry, subject to revocation.
- On 401 for a working credential: drop it, restart at Step 1.

Full API docs: https://docs.acmenotes.com/

## Errors

| Code | Where | What to do |
|------|-------|------------|
| `invalid_signature` | `/agent/auth` (ID-JAG) | Mint a fresh ID-JAG |
| `replay_detected` | `/agent/auth` (ID-JAG) | Mint with new `jti` |
| `audience_mismatch` | `/agent/auth` (ID-JAG) | Mint with `aud: "https://auth.acmenotes.com"` |
| `credential_expired` | `/agent/auth` (ID-JAG) | Mint a fresh one |
| `invalid_issuer` | `/agent/auth` (ID-JAG) | Provider not in trust list |
| `invalid_client_id` | `/agent/auth` (ID-JAG) | client_id doesn't resolve |
| `missing_verified_email` | `/agent/auth` (ID-JAG) | No verified contact in assertion |
| `anonymous_not_enabled` | `/agent/auth` | Use identity_assertion instead |
| `verified_email_not_enabled` | `/agent/auth` | Use id-jag or anonymous |
| `issuer_not_enabled` | `/agent/auth` | Provider not trusted — use email or anonymous |
| `unsupported_credential_type` | `/agent/auth` | Check metadata for supported types |
| `rate_limited` (429) | any | Back off and retry |
| `invalid_claim_token` | `/agent/auth/claim/complete` | Restart at Step 3 |
| `otp_invalid` | `/agent/auth/claim/complete` | Ask user to re-read code |
| `otp_expired` | `/agent/auth/claim/complete` | Re-trigger claim or restart |
| `claim_expired` | `/agent/auth/claim/complete` | Restart at Step 3 |
| `previously_claimed` | `/agent/auth/claim/complete` | Restart at Step 3 |

## Revocation

- **Provider-driven:** provider POSTs `logout+jwt` to `https://auth.acmenotes.com/agent/auth/revoke`. On next 401, restart at Step 1.
- **Email/anonymous:** on 401 for a working credential, drop it and restart at Step 1.
```

---

## Notes on This Example

1. **All three flows** are documented — in production, delete sections for unsupported flows
2. **Hostnames declared in intro** — resource server and auth server separated
3. **`Host:` headers in requests** — clarifies which server receives each call
4. **Expanded error table** — includes newer codes `invalid_issuer`, `invalid_client_id`, `missing_verified_email`
5. **Claim response for anonymous** — shows only `status: "claimed"` (scopes upgraded in place, no key rotation)
6. **Claim response for email** — shows credential issued in the response
