# Server-Side Implementation Guide

Detailed guidance for implementing auth.md protocol endpoints on the backend. Covers minimum implementation, security, rate limiting, audit events, and user matching.

---

## Table of Contents

1. [Minimum Implementation](#minimum-implementation)
2. [Discovery Documents](#discovery-documents)
3. [POST /agent/auth — Main Handler](#post-agentauth--main-handler)
4. [ID-JAG Verification](#id-jag-verification)
5. [OTP Ceremony](#otp-ceremony)
6. [Revocation](#revocation)
7. [User Matching and JIT Provisioning](#user-matching-and-jit-provisioning)
8. [Rate Limiting](#rate-limiting)
9. [Security](#security)
10. [Audit Events](#audit-events)
11. [Deploy Checklist](#deploy-checklist)

---

## Minimum Implementation

1. Publish `/.well-known/oauth-protected-resource` with an `agent_auth` block
2. Return `WWW-Authenticate: Bearer resource_metadata="..."` on 401 responses
3. Host a `/agent/auth` endpoint that dispatches on the `type` field
4. For agent verified: maintain a trust list of agent providers and verify ID-JAG signatures via JWKS
5. For user claimed: implement `/agent/auth/claim` and `/agent/auth/claim/complete`, and email OTPs to users
6. Issue credentials of the configured type (`access_token` or `api_key`)
7. For agent verified: accept revocation logout tokens at the advertised `revocation_uri`
8. Record audit events for every state change in the flow

---

## Discovery Documents

### Serving the PRM

```
GET /.well-known/oauth-protected-resource
→ 200 OK
→ Content-Type: application/json
```

The PRM is static and can be cached aggressively. Serve with `Cache-Control: public, max-age=3600`.

### Serving the AS Metadata

```
GET /.well-known/oauth-authorization-server
→ 200 OK
→ Content-Type: application/json
```

The AS metadata contains the `agent_auth` block. Can also be cached.

### WWW-Authenticate Header

On every 401 response from the API:

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: Bearer resource_metadata="https://api.service.com/.well-known/oauth-protected-resource"
```

This allows agents to bootstrap discovery without prior knowledge of the service.

---

## POST /agent/auth — Main Handler

All requests share the same endpoint and dispatch on the `type` field:

```
POST /agent/auth
Content-Type: application/json
```

### Dispatch

| `type` | `assertion_type` | Flow |
|--------|-----------------|------|
| `identity_assertion` | `urn:ietf:params:oauth:token-type:id-jag` | Agent Verified |
| `identity_assertion` | `verified_email` | User Claimed (email required) |
| `anonymous` | — | User Claimed (anonymous start) |

### Handler: identity_assertion + id-jag

1. Decode the ID-JAG header to obtain `kid` and `alg`
2. Look up the issuer (`iss`) in the trust list. Reject if unknown → `invalid_issuer`
3. Fetch JWKS from the provider (see ID-JAG Verification section for caching)
4. Verify signature using the key matching `kid` → `invalid_signature` if fails
5. Validate claims:
   - `aud` matches the auth server → `invalid_audience`
   - `exp` is in the future → `expired`
   - `iat` is not unreasonably in the future (accept ~1-2 min skew)
   - `jti` has not been seen recently → `replay_detected`
   - `client_id` resolves to a known provider identity → `invalid_client_id`
   - At least `email_verified` or `phone_number_verified` is `true` → `missing_verified_email`
6. Match or provision the user (see User Matching)
7. Issue credential of the requested type

### Handler: identity_assertion + verified_email

1. Create a registration row marked as `email-verification`
2. Persist the asserted email and requested credential type
3. Generate `claim_token` (returned to agent) and `claim_view_token` (delivered in email link)
4. Store SHA-256 hashes of both
5. Email the user a link to a server-rendered OTP page
6. Return claim handles — no credential

### Handler: anonymous

1. Apply rate limits
2. Create the principal that will own the credentials (user, workspace, account, etc.). Flag as agent-created
3. Issue an API key scoped to pre-claim (untrusted) permissions
4. Generate `claim_token` (prefixed, high-entropy — e.g., `clm_` + 25 chars base62). Store only its SHA-256 hash
5. Schedule an expiration job at the registration's TTL to revoke the API key and mark the claim expired

---

## ID-JAG Verification

### Trust List

Maintain a registry of providers whose assertions you accept. Minimum entry: an issuer URL. Richer entries can pin a JWKS URI, a CIMD URL, or an attestation policy (e.g., requires `mfa` in `amr`).

Treat this list as security-critical configuration — compromising a trusted provider means compromising every delegation routed through them.

### JWKS Fetching

- Fetch `{iss}/.well-known/jwks.json` on first use
- Cache per the response's `Cache-Control`, with a sane floor (10 min) and ceiling (24h)
- On `kid` cache miss, refetch once before rejecting — this handles provider key rotation gracefully

### CIMD Resolution

If `client_id` is a URL (not an opaque identifier):
1. Fetch it as an OAuth Client ID Metadata Document
2. Verify that `jwks_uri` matches the one used to verify the signature
3. This decouples provider identity from signing keys — rotation doesn't churn the trust list

### Replay Protection

- Cache of seen `jti` values with TTL of at least `exp - iat` + clock skew
- 5-minute assertion + 1 minute of skew → 6 minutes of cache
- Redis, Memcached, or an indexed database table with a TTL column
- Reject on collision with `replay_detected`

### Clock Skew

Accept `iat` up to ~1-2 minutes in the future to accommodate drift between provider and consumer clocks.

---

## OTP Ceremony

### POST /agent/auth/claim (anonymous start only)

1. Hash the incoming `claim_token` and look up the registration
2. Reject if not found (`invalid_claim_token`), already claimed (`claimed_or_in_flight`), or expired (`claim_expired`)
3. Mint a `claim_view_token`, store its SHA-256 hash
4. Email the user a link that includes the plaintext token
5. The link lands on a service-hosted page that renders the OTP
6. Communicate to the user that an agent is requesting ownership — make it easy to reject if unrecognized

### POST /agent/auth/claim/complete

1. Hash both the `claim_token` and the OTP, compare to stored hashes
2. Reject with:
   - `otp_invalid` (401) — wrong code
   - `otp_expired` (410) — code expired
   - `previously_claimed` (409) — already claimed
   - `claim_expired` (410) — registration expired
3. **Anonymous start:** link the existing credential to the matched/JIT'd user, replace its scope set with `post_claim_scopes`, do NOT rotate the token. Agent keeps the same key.
4. **Email required:** issue a fresh credential of the type requested at registration

### Why in-place permission swap on anonymous?

- Agent doesn't need to handle a rotation flow or poll for a new key
- No race window between claim confirmation and the agent discovering it needs to re-exchange
- Consistent with how most permission systems operate (IAM, RBAC, GitHub PATs)
- Trade-off: anyone who captured the key pre-claim retains access post-claim with new scopes
- For security-sensitive tenants: offer forced-rotation as an opt-in

---

## Revocation

### Receiving Logout Tokens

```
POST /agent/auth/revoke
Content-Type: application/logout+jwt
```

Body is a JWT signed by the provider.

### Processing

1. Verify the logout token's signature against the issuer's JWKS (same trust path as ID-JAG verification)
2. Enforce `jti` uniqueness for replay protection
3. Find all credentials issued for `(iss, sub, aud)` and invalidate them
4. Return 200 on success, 400 on verification failure

### Extensibility

Expect to extend this surface with SET (RFC 8417) / CAEP / RISC event communication for session changes beyond revocation, delivered via webhook or SSE.

---

## User Matching and JIT Provisioning

Recommended resolution order:

1. **Delegation record match** — if credentials were previously issued for this `(iss, sub)`, route to the same user. This is the strongest identifier.
2. **Verified email match** — if a user exists with the same verified email, link. Note: `email_verified: true` in the ID-JAG reflects the provider's verification, which you may or may not accept as sufficient.
3. **Verified phone match** — same pattern.
4. **No match → JIT** — create a new user per provisioning policy, or refuse if the product requires manual onboarding.

Reject ID-JAGs with neither a verified email nor a verified phone — there's no basis for matching and no channel for user-facing communications.

---

## Rate Limiting

### Two Tiers

| Tier | Checked | Default Anonymous | Default Identity Assertion |
|------|---------|-------------------|---------------------------|
| Per-IP | First | 5/hour | 60/hour |
| Per-tenant | Second | 100/hour | 1000/hour |

### Implementation

- Sliding-window counter with a shared store (Redis is common)
- Fail open on store errors to avoid blocking legitimate traffic
- If IP is not available (stripped by proxy), skip per-IP check rather than rejecting
- Return 429 with `Retry-After` header

---

## Security

### Token Hashing

| Token | Storage | Plaintext leaves server |
|-------|---------|------------------------|
| `claim_token` | SHA-256 hash | Once, in the `/agent/auth` response |
| `claim_view_token` | SHA-256 hash | Once, in the email link |
| OTP | SHA-256 hash | Once, on the claim view page |

### OTP

- Use CSPRNG (`crypto.randomInt` or equivalent)
- 6 digits
- TTL ≤10 minutes
- Tight retry limits (3-5 attempts)
- Lockout after exceeding attempts

### IP Logging

Capture IPs at:
- Registration
- Claim initiation
- Claim complete

### Scope on /claim and /complete

Both endpoints are public but must resolve to a tenant/environment, and reject tokens that don't belong to that scope.

### Bulk Revocation

Provide an operator-facing mechanism to revoke all outstanding agent credentials for a tenant in one shot — for incident response.

### Assertion Replay

`jti` cache is mandatory. Shared store required if `/agent/auth` runs across multiple replicas.

### Trust List Discipline

Treat the trusted-providers list as security-critical configuration. Changes should be audited and rolled out with the same care as any auth config change.

---

## Audit Events

### Recommended Events

| Event | When | Minimum Data |
|-------|------|--------------|
| `registration.created` | Successful POST /agent/auth | registration_id, registration_type, iss, sub (if ID-JAG) |
| `claim.requested` | /agent/auth/claim called (or implicit on email-verif) | registration_id, email |
| `otp.generated` | OTP minted for claim view | registration_id |
| `claim.confirmed` | /agent/auth/claim/complete succeeds | registration_id, claimed_by_user_id |
| `registration.expired` | Unclaimed registration past TTL | registration_id |
| `registration.revoked` | Logout token processed | registration_id, iss, sub |

### Additional Metadata for ID-JAG Flows

Include `iss`, `sub`, `agent_platform`, and `agent_context_id` for correlation with provider-side logs.

### Resource Tagging

Services that already expose resource events (API keys, invitations, membership) should consider tagging with:
- `created_by_agent: true`
- `status`: `unclaimed` / `claimed` / `expired`

---

## Deploy Checklist

### Before publishing

- [ ] PRM served at `/.well-known/oauth-protected-resource` with valid JSON
- [ ] AS metadata served at `/.well-known/oauth-authorization-server` with `agent_auth` block
- [ ] `auth.md` served at the domain root
- [ ] API returns `WWW-Authenticate` header on 401s
- [ ] `/agent/auth` accepts POST and dispatches correctly by `type`
- [ ] Trust list configured (if agent verified)
- [ ] JWKS fetching implemented with cache (if agent verified)
- [ ] `/agent/auth/claim` and `/agent/auth/claim/complete` implemented (if user claimed)
- [ ] Email sending configured for OTPs (if user claimed)
- [ ] `/agent/auth/revoke` accepts logout tokens (if agent verified)
- [ ] Rate limiting active on `/agent/auth`
- [ ] Tokens stored as SHA-256 hashes
- [ ] Replay protection for `jti` implemented
- [ ] Audit events being recorded
- [ ] OTP with CSPRNG, TTL ≤10 min, retry limits

### Recommended tests

- [ ] Agent verified: valid ID-JAG → credential issued
- [ ] Agent verified: expired ID-JAG → `expired` error
- [ ] Agent verified: repeated `jti` → `replay_detected` error
- [ ] Agent verified: unknown issuer → `invalid_issuer` error
- [ ] Anonymous: registration → credential with pre-claim scopes
- [ ] Anonymous: claim complete → scopes upgraded
- [ ] Email required: registration → no credential, email sent
- [ ] Email required: claim complete → credential issued
- [ ] Wrong OTP → `otp_invalid` error
- [ ] Expired OTP → `otp_expired` error
- [ ] Rate limit exceeded → 429
- [ ] Valid logout token → credentials revoked
- [ ] 401 on API → WWW-Authenticate header present
