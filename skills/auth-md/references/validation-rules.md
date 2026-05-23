# Validation Rules

Complete ruleset for validating `auth.md` files against the protocol specification.

---

## Level: Basic (Offline)

### Structure Rules

| ID | Rule | Error Message | Severity |
|----|------|---------------|----------|
| S01 | Document starts with `# auth.md` heading | Missing required top-level heading `# auth.md` | 🔴 |
| S02 | Contains `## Step 1 — Discover` section | Missing required section: Step 1 — Discover | 🔴 |
| S03 | Contains `## Step 2 — Pick a method` section | Missing required section: Step 2 — Pick a method | 🔴 |
| S04 | Contains `## Step 3 — Register` section | Missing required section: Step 3 — Register | 🔴 |
| S05 | Contains `## Step 4 — Claim ceremony` section (if user claimed flow) | Missing required section: Step 4 — Claim ceremony (required when user claimed flow is supported) | 🔴 |
| S06 | Contains `## Step 5 — Use the credential` section | Missing required section: Step 5 — Use the credential | 🔴 |
| S07 | Contains `## Errors` section | Missing required section: Errors | 🔴 |
| S08 | Contains `## Revocation` section | Missing required section: Revocation | 🔴 |
| S09 | Sections appear in correct order (Step 1 → 2 → 3 → 4 → 5 → Errors → Revocation) | Sections are out of order. Expected sequence: Step 1, 2, 3, 4, 5, Errors, Revocation | 🟡 |
| S10 | Intro declares real hostnames (resource server and auth server) | Intro should declare real hostnames for resource and auth servers | 🟢 |

### Field Rules

| ID | Rule | Error Message | Severity |
|----|------|---------------|----------|
| F01 | Step 1 contains at least one fenced JSON block with `resource` field | Step 1 must include Protected Resource Metadata JSON with `resource` field | 🔴 |
| F02 | JSON metadata contains `resource_name` | Missing `resource_name` in metadata — agents need this for consent prompts | 🟡 |
| F03 | JSON metadata contains `authorization_servers` array | Missing `authorization_servers` — agents can't discover the auth endpoint | 🟡 |
| F04 | JSON metadata contains `scopes_supported` array with ≥1 scope | Missing or empty `scopes_supported` | 🟡 |
| F05 | JSON metadata contains `agent_auth` block | Missing `agent_auth` block in Authorization Server metadata | 🔴 |
| F06 | `agent_auth` contains `register_uri` | Missing `register_uri` in agent_auth block | 🔴 |
| F07 | `agent_auth` contains `identity_types_supported` with ≥1 type | Missing or empty `identity_types_supported` | 🔴 |
| F08 | If `identity_assertion` in identity_types_supported, `assertion_types_supported` must exist | Declared `identity_assertion` support but missing `assertion_types_supported` | 🟡 |
| F09 | If `anonymous` in identity_types_supported, `anonymous.credential_types_supported` must exist | Declared `anonymous` support but missing credential types for anonymous flow | 🟡 |
| F10 | Step 3 contains at least one `POST /agent/auth` request example | Step 3 must include at least one registration request example | 🔴 |
| F11 | Errors section contains a table with `Code`, `Where`, and `What to do` columns | Errors section must contain a table with Code, Where, and What to do columns | 🟡 |
| F12 | `agent_auth` contains `skill` pointing to auth.md URL | Missing `skill` field in agent_auth — recommended for discoverability | 🟢 |
| F13 | `agent_auth` contains `events_supported` | Missing `events_supported` — recommended for revocation support | 🟢 |
| F14 | If agent verified flow, `revocation_uri` must exist | Agent verified flow requires `revocation_uri` for logout token reception | 🟡 |
| F15 | If user claimed flow, `claim_uri` must exist | User claimed flow requires `claim_uri` for OTP ceremony | 🟡 |

### Consistency Rules

| ID | Rule | Error Message | Severity |
|----|------|---------------|----------|
| C01 | Flows documented in Step 3 match `identity_types_supported` in metadata | Mismatch: Step 3 documents flows not declared in metadata (or vice versa) | 🟡 |
| C02 | If only agent verified flow: Step 4 may be absent or explicitly marked N/A | Step 4 present but only agent verified flow is supported — remove or mark N/A | 🟡 |
| C03 | `register_uri` path matches the POST path in Step 3 examples | Registration URI in metadata doesn't match the POST path in Step 3 | 🟡 |
| C04 | `claim_uri` path matches the POST path in Step 4 examples (if present) | Claim URI in metadata doesn't match the POST path in Step 4 | 🟡 |
| C05 | Scopes in response examples are subset of `scopes_supported` | Response example contains scopes not listed in `scopes_supported` | 🟡 |
| C06 | `resource` URL is consistent across all JSON blocks | Different `resource` URLs found in metadata blocks — must be consistent | 🟡 |
| C07 | All URLs use HTTPS scheme | Non-HTTPS URL found — all endpoints must use HTTPS | 🟡 |
| C08 | Error codes in table match standard protocol error codes | Non-standard error code found | 🟡 |
| C09 | `aud` in ID-JAG examples matches the `auth_server_url` | ID-JAG `aud` example doesn't match the authorization server URL | 🟡 |
| C10 | `assertion_types_supported` includes types used in Step 3 examples | Step 3 uses assertion types not declared in metadata | 🟡 |

### Format Rules

| ID | Rule | Error Message | Severity |
|----|------|---------------|----------|
| X01 | All JSON in fenced code blocks is valid JSON | Invalid JSON in fenced code block at section: {section} | 🟡 |
| X02 | HTTP request examples use valid HTTP method + path | Invalid HTTP request format. Expected: METHOD /path | 🟡 |
| X03 | No unreplaced placeholder patterns (`{{...}}`, `<your-...>`, `[YOUR_...]`) | Unreplaced placeholder found: {placeholder} | 🟡 |
| X04 | Fenced code blocks have language hint (`http`, `json`) | Code block missing language hint — agents use these to identify request shapes | 🟢 |

---

## Level: Full (Live)

All Basic rules, plus:

### Endpoint Rules

| ID | Rule | Error Message | Severity |
|----|------|---------------|----------|
| E01 | `GET {base_url}/.well-known/oauth-protected-resource` returns 200 with valid JSON | Protected Resource Metadata endpoint not reachable or returns invalid response | 🔴 |
| E02 | PRM response contains `agent_auth` block or points to AS that does | No `agent_auth` block discoverable through PRM → AS metadata chain | 🔴 |
| E03 | `GET {auth_server}/.well-known/oauth-authorization-server` returns 200 with valid JSON | Authorization Server metadata endpoint not reachable | 🔴 |
| E04 | AS metadata `agent_auth` block matches declarations in auth.md | Live AS metadata `agent_auth` block differs from auth.md declarations | 🟡 |
| E05 | `register_uri` accepts POST (returns 400/401/422, not 404/405) | Registration endpoint returns 404 or 405 — not implemented | 🔴 |
| E06 | `claim_uri` accepts POST (if user claimed flow supported) | Claim endpoint returns 404 or 405 — not implemented | 🔴 |
| E07 | `revocation_uri` accepts POST (if agent verified flow supported) | Revocation endpoint returns 404 or 405 — not implemented | 🔴 |
| E08 | API base URL returns 401 with `WWW-Authenticate` header containing `resource_metadata` | API does not return WWW-Authenticate header with resource_metadata on 401 | 🟡 |

---

## Standard Protocol Error Codes

Complete list of error codes the Errors table should cover (per supported flows):

### Agent Verified Flow
- `invalid_signature`
- `replay_detected`
- `audience_mismatch` / `invalid_audience`
- `credential_expired` / `expired`
- `invalid_issuer`
- `invalid_client_id`
- `missing_verified_email`
- `unsupported_credential_type`
- `insufficient_user_authentication`
- `rate_limited`

### User Claimed Flow
- `anonymous_not_enabled`
- `verified_email_not_enabled`
- `unsupported_credential_type`
- `rate_limited`
- `invalid_claim_token`
- `otp_invalid`
- `otp_expired`
- `claim_expired`
- `previously_claimed`

### Both Flows
- `issuer_not_enabled`
- `rate_limited`

---

## Validation Report Format

```markdown
# Validation Report — auth.md

**File:** {path_or_url}
**Level:** {basic|full}
**Date:** {timestamp}

## Summary

- ✅ {n} rules passed
- ❌ {n} rules failed
- ⚠️ {n} warnings

## Structure

| Status | ID | Rule |
|--------|-----|-------|
| ✅ | S01 | Heading `# auth.md` present |
| ❌ | S05 | Step 4 section missing (user claimed flow declared) |

## Fields
...

## Consistency
...

## Format
...

## Endpoints (full only)
...
```
