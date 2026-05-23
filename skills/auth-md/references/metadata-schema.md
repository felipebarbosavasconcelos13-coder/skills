# Metadata Schema

JSON structure reference for the discovery documents and tokens required by the auth.md protocol.

---

## Protected Resource Metadata (PRM)

Served at: `{resource_server}/.well-known/oauth-protected-resource`

Defined by [RFC 9728](https://datatracker.ietf.org/doc/html/rfc9728).

```json
{
  "resource": "https://api.example.com/",
  "resource_name": "Example Service",
  "resource_logo_uri": "https://example.com/logo.png",
  "authorization_servers": ["https://auth.example.com/"],
  "scopes_supported": ["read", "write", "admin"],
  "bearer_methods_supported": ["header"]
}
```

### Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `resource` | ✅ | string (URL) | Canonical URL of the API. Used as `aud` in ID-JAGs. |
| `resource_name` | ✅ | string | Display name for consent prompts. |
| `resource_logo_uri` | Recommended | string (URL) | Logo for consent UI. |
| `authorization_servers` | ✅ | string[] | Base URLs of OAuth AS(s). Agent fetches AS metadata from here. |
| `scopes_supported` | ✅ | string[] | All scopes the resource server understands. |
| `bearer_methods_supported` | ✅ | string[] | How credentials are presented. Typically `["header"]`. |

---

## Authorization Server Metadata

Served at: `{authorization_server}/.well-known/oauth-authorization-server`

```json
{
  "resource": "https://api.example.com/",
  "authorization_servers": ["https://auth.example.com/"],
  "scopes_supported": ["read", "write", "admin"],
  "bearer_methods_supported": ["header"],
  "agent_auth": {
    "skill": "https://example.com/auth.md",
    "register_uri": "https://auth.example.com/agent/auth",
    "claim_uri": "https://auth.example.com/agent/auth/claim",
    "revocation_uri": "https://auth.example.com/agent/auth/revoke",
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
```

### `agent_auth` Block Fields

| Field | Required | Type | Description |
|-------|----------|------|-------------|
| `skill` | Recommended | string (URL) | URL of the auth.md file. |
| `register_uri` | ✅ | string (URL) | POST endpoint for registration. |
| `claim_uri` | Conditional | string (URL) | POST endpoint for claim initiation. Required if user claimed flow supported. |
| `revocation_uri` | Conditional | string (URL) | POST endpoint receiving logout tokens. Required if agent verified flow supported. |
| `identity_types_supported` | ✅ | string[] | `"anonymous"`, `"identity_assertion"`, or both. |
| `anonymous` | Conditional | object | Required if `"anonymous"` in identity_types_supported. |
| `anonymous.credential_types_supported` | ✅ (if anonymous) | string[] | `"api_key"` and/or `"access_token"`. |
| `identity_assertion` | Conditional | object | Required if `"identity_assertion"` in identity_types_supported. |
| `identity_assertion.assertion_types_supported` | ✅ (if identity_assertion) | string[] | `"urn:ietf:params:oauth:token-type:id-jag"` and/or `"verified_email"`. |
| `identity_assertion.credential_types_supported` | ✅ (if identity_assertion) | string[] | `"api_key"` and/or `"access_token"`. |
| `events_supported` | Recommended | string[] | Security event schemas the service can ingest. |

### identity_types → Conceptual Flow Mapping

| `identity_types_supported` | `assertion_types_supported` | Flow |
|---|---|---|
| `identity_assertion` | `urn:ietf:params:oauth:token-type:id-jag` | Agent Verified |
| `identity_assertion` | `verified_email` | User Claimed (email required) |
| `anonymous` | — | User Claimed (anonymous start) |

---

## ID-JAG Token (Identity Assertion JWT Authorization Grant)

Defined by [draft-ietf-oauth-identity-assertion-authz-grant](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-identity-assertion-authz-grant).

### Header

```json
{
  "typ": "oauth-id-jag+jwt",
  "alg": "ES256",
  "kid": "<provider-key-id>"
}
```

### Payload

```json
{
  "iss": "https://api.agent-provider.com",
  "sub": "<opaque-user-identifier>",
  "aud": "https://auth.example.com",
  "client_id": "<issuer-url-or-cimd-url>",
  "jti": "<unique-token-id>",
  "iat": 1716400000,
  "exp": 1716400300,
  "email": "user@example.com",
  "email_verified": true,
  "amr": ["mfa"],
  "auth_time": 1716399000,
  "name": "Jane Smith",
  "phone_number": "+15553805188",
  "phone_number_verified": false,
  "resource": "https://api.example.com",
  "agent_platform": "cursor",
  "agent_context_id": "chat-abc123"
}
```

### Required Claims

| Claim | Description |
|-------|-------------|
| `iss` | Provider's issuer URL (must be on service's trust list) |
| `sub` | Opaque user identifier at the provider |
| `aud` | Service's authorization server URL |
| `client_id` | Provider identity (issuer URL or CIMD URL) |
| `jti` | Unique token ID for replay protection |
| `iat` | Issuance time (epoch seconds) |
| `exp` | Expiration (typically iat + 5 minutes) |
| `email` + `email_verified: true` OR `phone_number` + `phone_number_verified: true` | At least one verified contact required |

### Optional Claims

| Claim | Description |
|-------|-------------|
| `amr` | Authentication methods reference (e.g., `["mfa"]`) |
| `auth_time` | Original authentication time (epoch seconds) |
| `name` | User's display name |
| `phone_number` | User's phone number |
| `phone_number_verified` | Whether phone is verified |
| `resource` | Resource server URL (informational) |
| `agent_platform` | Agent platform (e.g., `"cursor"`, `"chatgpt"`) |
| `agent_context_id` | Agent context/chat ID |

---

## Client ID Metadata Document (CIMD)

Optional document that decouples provider identity from signing keys. Defined by [draft-ietf-oauth-client-id-metadata-document](https://datatracker.ietf.org/doc/draft-ietf-oauth-client-id-metadata-document/).

Hosted at the URL used as `client_id` in the ID-JAG.

```json
{
  "client_id": "https://api.agent-provider.com/agent-auth.json",
  "client_name": "Agent Provider",
  "logo_uri": "https://agent-provider.com/logo.png",
  "client_uri": "https://agent-provider.com",
  "tos_uri": "https://agent-provider.com/tos",
  "policy_uri": "https://agent-provider.com/privacy",
  "token_endpoint_auth_method": "private_key_jwt",
  "jwks_uri": "https://agent-provider.com/.well-known/jwks.json",
  "scope": "openid email profile"
}
```

### Benefits of CIMD

- JWKS rotation without churning consumer trust lists
- Facilitates listing in trusted agent registries
- Provides rich metadata about the provider (logo, ToS, privacy policy)

---

## Registration Response Shapes

### Agent Verified (success)

```json
{
  "registration_id": "reg_01ABC123DEF456",
  "registration_type": "agent-provider",
  "credential_type": "access_token",
  "credential": "eyJhbGciOiJSUzI1NiJ9...",
  "credential_expires": "2026-05-22T13:00:00.000Z",
  "scopes": ["read", "write"]
}
```

Variant with `api_key`:

```json
{
  "registration_id": "reg_01ABC123DEF456",
  "registration_type": "agent-provider",
  "credential_type": "api_key",
  "credential": "acme_key_live_EXAMPLE_00000000",
  "credential_expires": null,
  "scopes": ["read", "write"]
}
```

### Anonymous Start (success)

```json
{
  "registration_id": "reg_01GHI345JKL678",
  "registration_type": "anonymous",
  "credential_type": "api_key",
  "credential": "acme_key_test_EXAMPLE_00000000",
  "credential_expires": null,
  "scopes": ["read"],
  "claim_url": "/agent/auth/claim",
  "claim_token": "clm_abc123def456ghi789jkl012mno",
  "claim_token_expires": "2026-05-22T12:00:00.000Z",
  "post_claim_scopes": ["read", "write"]
}
```

### Email Required (success)

```json
{
  "registration_id": "reg_01DEF789GHI012",
  "registration_type": "email-verification",
  "claim_url": "/agent/auth/claim",
  "claim_token": "clm_xYz789AbC012dEf",
  "claim_token_expires": "2026-05-22T12:00:00.000Z",
  "post_claim_scopes": ["read", "write"]
}
```

### Claim Initiation (anonymous → /agent/auth/claim)

```json
{
  "registration_id": "reg_01GHI345JKL678",
  "claim_attempt_id": "att_001",
  "status": "initiated",
  "expires_at": "2026-05-22T12:10:00.000Z"
}
```

### Claim Complete — Anonymous (scopes upgraded in place)

```json
{
  "registration_id": "reg_01GHI345JKL678",
  "status": "claimed"
}
```

### Claim Complete — Email Required (credential issued)

```json
{
  "registration_id": "reg_01DEF789GHI012",
  "status": "claimed",
  "credential_type": "access_token",
  "credential": "<token>",
  "credential_expires": "2026-05-22T13:00:00.000Z",
  "scopes": ["read", "write"]
}
```

---

## Error Response Shape

```json
{
  "error": "<error_code>",
  "message": "<human-readable description>"
}
```

---

## Logout Token (Revocation)

Sent by the provider to the service's `revocation_uri`.

### Header

```json
{
  "typ": "logout+jwt",
  "alg": "ES256",
  "kid": "<provider-key-id>"
}
```

### Payload

```json
{
  "iss": "https://api.agent-provider.com",
  "sub": "<opaque-user-identifier>",
  "aud": "https://auth.example.com",
  "jti": "<unique-identifier>",
  "iat": 1716400000,
  "events": {
    "https://schemas.workos.com/events/agent/auth/identity/assertion/revoked": {}
  }
}
```

### Processing

1. Verify signature against the issuer's JWKS (same trust path as ID-JAG verification)
2. Enforce `jti` uniqueness for replay protection
3. Find all credentials issued for `(iss, sub, aud)` and invalidate them
4. Return 200 on success, 400 on verification failure
