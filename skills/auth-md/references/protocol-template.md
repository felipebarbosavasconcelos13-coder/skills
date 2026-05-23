# Protocol Template

Canonical template for generating an `auth.md` file. Replace all `{{placeholders}}` with service-specific values. Delete sections for flows the service does not support.

---

## File Structure

An auth.md is organized as a numbered walkthrough the agent follows top to bottom:

1. **Title and intro** — addressed to the agent, declares real hostnames (resource server + auth server)
2. **Step 1 — Discover** — two-hop discovery (PRM → AS metadata)
3. **Step 2 — Pick a method** — decision tree
4. **Step 3 — Register** — one subsection per supported method
5. **Step 4 — Claim ceremony** — OTP exchange (if user claimed)
6. **Step 5 — Use the credential** — how to use and renew
7. **Errors** — error codes table
8. **Revocation** — how credentials are invalidated

---

## Complete Template

```markdown
# auth.md

You are an agent. This service supports **agentic registration**: discover → register → (claim if needed) → call API → handle revocation. Follow the steps in order; do not skip ahead.

The resource server is `{{base_url}}` and the authorization server is `{{auth_server_url}}`.

## Step 1 — Discover

Discovery is two hops. The 401 response that pointed you here carries a `WWW-Authenticate` header with the PRM URL:

\```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer resource_metadata="{{base_url}}/.well-known/oauth-protected-resource"
\```

### 1a. Fetch the Protected Resource Metadata

\```http
GET /.well-known/oauth-protected-resource
Host: {{resource_host}}
\```

Response:

\```json
{
  "resource": "{{base_url}}/",
  "resource_name": "{{service_name}}",
  "resource_logo_uri": "{{logo_url}}",
  "authorization_servers": ["{{auth_server_url}}/"],
  "scopes_supported": [{{scopes_list}}],
  "bearer_methods_supported": ["header"]
}
\```

### 1b. Fetch the Authorization Server metadata

\```http
GET /.well-known/oauth-authorization-server
Host: {{auth_server_host}}
\```

Response:

\```json
{
  "resource": "{{base_url}}/",
  "authorization_servers": ["{{auth_server_url}}/"],
  "scopes_supported": [{{scopes_list}}],
  "bearer_methods_supported": ["header"],
  "agent_auth": {
    "skill": "{{auth_md_url}}",
    "register_uri": "{{auth_server_url}}/agent/auth",
    "claim_uri": "{{auth_server_url}}/agent/auth/claim",
    "revocation_uri": "{{auth_server_url}}/agent/auth/revoke",
    "identity_types_supported": [{{identity_types}}],
    "anonymous": {
      "credential_types_supported": [{{anonymous_credential_types}}]
    },
    "identity_assertion": {
      "assertion_types_supported": [{{assertion_types}}],
      "credential_types_supported": [{{assertion_credential_types}}]
    },
    "events_supported": [
      "https://schemas.workos.com/events/agent/auth/identity/assertion/revoked"
    ]
  }
}
\```

## Step 2 — Pick a method

Use this decision tree:

1. **You have a session tied to a user identity and can exchange it for an ID-JAG, audience-bound to this service** → identity_assertion + id-jag.
2. **You have only the user's email** → identity_assertion + email. Claim ceremony required.
3. **You have neither** → anonymous. Claim ceremony optional.

Before sending: cross-check your choice against the `agent_auth` block. If the matching `*_supported` array doesn't list your method, pick another or stop.

## Step 3 — Register

Before sending an `identity_assertion`, surface the service's `resource_name` and `resource_logo_uri` and confirm with the user. Skip this for `anonymous`.

### identity_assertion + id-jag

<!-- DELETE THIS SECTION IF NOT SUPPORTING AGENT VERIFIED FLOW -->

Mint the ID-JAG with `aud: "{{auth_server_url}}"`.

\```http
POST /agent/auth
Host: {{auth_server_host}}
Content-Type: application/json

{
  "type": "identity_assertion",
  "assertion_type": "urn:ietf:params:oauth:token-type:id-jag",
  "assertion": "<ID-JAG>",
  "requested_credential_type": "{{id_jag_credential_type}}"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_...",
  "registration_type": "agent-provider",
  "credential_type": "{{id_jag_credential_type}}",
  "credential": "<token>",
  "credential_expires": "{{expiry_or_null}}",
  "scopes": [{{post_registration_scopes}}]
}
\```

Go to Step 5.

### identity_assertion + email

<!-- DELETE THIS SECTION IF NOT SUPPORTING USER CLAIMED EMAIL REQUIRED FLOW -->

\```http
POST /agent/auth
Host: {{auth_server_host}}
Content-Type: application/json

{
  "type": "identity_assertion",
  "assertion_type": "verified_email",
  "assertion": "user@example.com",
  "requested_credential_type": "{{email_credential_type}}"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_...",
  "registration_type": "email-verification",
  "claim_url": "{{auth_server_url}}/agent/auth/claim",
  "claim_token": "clm_...",
  "claim_token_expires": "{{claim_ttl}}",
  "post_claim_scopes": [{{post_claim_scopes}}]
}
\```

No credential yet. Go to Step 4.

### anonymous

<!-- DELETE THIS SECTION IF NOT SUPPORTING USER CLAIMED ANONYMOUS START FLOW -->

\```http
POST /agent/auth
Host: {{auth_server_host}}
Content-Type: application/json

{
  "type": "anonymous",
  "requested_credential_type": "{{anonymous_credential_type}}"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_...",
  "registration_type": "anonymous",
  "credential_type": "{{anonymous_credential_type}}",
  "credential": "sk_test_...",
  "credential_expires": null,
  "scopes": [{{pre_claim_scopes}}],
  "claim_url": "{{auth_server_url}}/agent/auth/claim",
  "claim_token": "clm_...",
  "claim_token_expires": "{{claim_ttl}}",
  "post_claim_scopes": [{{post_claim_scopes}}]
}
\```

Credential works immediately at pre-claim scopes. To upgrade, go to Step 4.

## Step 4 — Claim ceremony

<!-- DELETE THIS ENTIRE SECTION IF ONLY SUPPORTING AGENT VERIFIED FLOW -->

### 4a. Trigger the claim email (anonymous only)

Skip for email registrations — email was sent in Step 3.

\```http
POST /agent/auth/claim
Host: {{auth_server_host}}
Content-Type: application/json

{
  "claim_token": "clm_...",
  "email": "user@example.com"
}
\```

Response (200):

\```json
{
  "registration_id": "reg_...",
  "claim_attempt_id": "att_...",
  "status": "initiated",
  "expires_at": "{{claim_attempt_ttl}}"
}
\```

### 4b. Wait for the user's OTP

Surface in your agent UI:
- Default: "Check your email from {{service_name}} and tell me the 6-digit code."
- If URL pasted: "Open the link in your browser — the page will show a 6-digit code."
- If rejected: "That code didn't work — re-read it carefully."

### 4c. Submit the OTP

\```http
POST /agent/auth/claim/complete
Host: {{auth_server_host}}
Content-Type: application/json

{
  "claim_token": "clm_...",
  "otp": "123456"
}
\```

Response (anonymous — scopes upgraded in place):

\```json
{
  "registration_id": "reg_...",
  "status": "claimed"
}
\```

Response (email-verification — credential issued):

\```json
{
  "registration_id": "reg_...",
  "status": "claimed",
  "credential_type": "{{email_credential_type}}",
  "credential": "<token>",
  "credential_expires": "{{expiry_or_null}}",
  "scopes": [{{post_claim_scopes}}]
}
\```

## Step 5 — Use the credential

Present as a bearer token:

\```http
GET /api/some-resource
Host: {{resource_host}}
Authorization: Bearer <credential>
\```

- `access_token`: when expired, mint a fresh ID-JAG and re-register. No refresh flow.
- `api_key`: no expiry, subject to revocation.
- On 401 for a previously-working credential: drop it, restart at Step 1.

## Errors

| Code | Where | What to do |
|------|-------|------------|
| `invalid_signature` | `/agent/auth` (ID-JAG) | Mint a fresh ID-JAG |
| `replay_detected` | `/agent/auth` (ID-JAG) | Mint with new `jti` |
| `audience_mismatch` | `/agent/auth` (ID-JAG) | Mint with correct `aud` |
| `credential_expired` | `/agent/auth` (ID-JAG) | Mint a fresh one |
| `invalid_issuer` | `/agent/auth` (ID-JAG) | Provider not in trust list |
| `invalid_client_id` | `/agent/auth` (ID-JAG) | client_id doesn't resolve |
| `missing_verified_email` | `/agent/auth` (ID-JAG) | No verified contact in assertion |
| `insufficient_user_authentication` | `/agent/auth` (ID-JAG) | Auth context didn't meet policy (RFC 9470) |
| `anonymous_not_enabled` | `/agent/auth` | Pick another method |
| `verified_email_not_enabled` | `/agent/auth` | Pick another method |
| `issuer_not_enabled` | `/agent/auth` | Provider not trusted. Pick another method |
| `unsupported_credential_type` | `/agent/auth` | Re-read metadata and adjust |
| `rate_limited` (429) | any | Back off and retry |
| `invalid_claim_token` | `/agent/auth/claim/complete` | Restart at Step 3 |
| `otp_invalid` | `/agent/auth/claim/complete` | Ask user to re-read code |
| `otp_expired` | `/agent/auth/claim/complete` | Re-trigger claim email or restart |
| `claim_expired` | `/agent/auth/claim/complete` | Restart at Step 3 |
| `previously_claimed` | `/agent/auth/claim/complete` | Restart at Step 3 for fresh credential |

## Revocation

- **Provider-driven (ID-JAG flows):** provider POSTs `logout+jwt` to `{{auth_server_url}}/agent/auth/revoke`. Credential invalidated. On next 401, restart at Step 1.
- **Email/anonymous flows:** no agent-facing revoke endpoint. On 401, drop credential and restart at Step 1.
```

---

## Placeholder Reference

| Placeholder | Description | Example |
|-------------|-------------|---------|
| `{{base_url}}` | API base URL (resource server) | `https://api.acme.com` |
| `{{resource_host}}` | Resource server host | `api.acme.com` |
| `{{service_name}}` | Human-readable service name | `Acme Notes` |
| `{{logo_url}}` | Service logo URL | `https://acme.com/logo.png` |
| `{{auth_server_url}}` | Authorization server base URL | `https://auth.acme.com` |
| `{{auth_server_host}}` | Authorization server host | `auth.acme.com` |
| `{{auth_md_url}}` | URL where auth.md is hosted | `https://acme.com/auth.md` |
| `{{scopes_list}}` | JSON array of scope strings | `"notes.read", "notes.write"` |
| `{{identity_types}}` | Supported identity types | `"anonymous", "identity_assertion"` |
| `{{assertion_types}}` | Supported assertion types | `"urn:ietf:params:oauth:token-type:id-jag", "verified_email"` |
| `{{anonymous_credential_types}}` | Credential types for anonymous | `"api_key"` |
| `{{assertion_credential_types}}` | Credential types for assertions | `"access_token", "api_key"` |
| `{{id_jag_credential_type}}` | Credential type for ID-JAG flow | `access_token` |
| `{{email_credential_type}}` | Credential type for email flow | `api_key` |
| `{{anonymous_credential_type}}` | Credential type for anonymous flow | `api_key` |
| `{{pre_claim_scopes}}` | Scopes before claim (anonymous) | `"notes.read"` |
| `{{post_claim_scopes}}` | Scopes after claim | `"notes.read", "notes.write"` |
| `{{post_registration_scopes}}` | Scopes after ID-JAG registration | `"notes.read", "notes.write"` |
| `{{claim_ttl}}` | Claim token expiration (ISO timestamp) | `2026-05-22T12:00:00.000Z` |
| `{{claim_attempt_ttl}}` | Claim attempt expiration | `2026-05-22T12:10:00.000Z` |
| `{{expiry_or_null}}` | Credential expiry or `null` | `"2026-05-22T13:00:00.000Z"` |

---

## Generation Rules

1. **Keep the file concise and high-signal** — anything the agent doesn't need to register or operate belongs in main documentation, not in auth.md
2. **Use fenced code blocks with language hints** (`http`, `json`) so agents can extract templates unambiguously
3. **Declare real hostnames in the intro** — resource server and auth server — so the agent knows which host each example targets
4. **Delete sections for unsupported flows** — don't leave empty sections or "N/A" markers
5. **The PRM is authoritative** — if anything in auth.md conflicts with the PRM, the PRM wins
