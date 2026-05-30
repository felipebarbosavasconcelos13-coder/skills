---
title: "Global Privacy Control (GPC)"
category: privacy
status: recommended
updated: "2026-05-30T14:20:00.000Z"
sources:
  - title: "Global Privacy Control specification"
    url: "https://globalprivacycontrol.github.io/gpc-spec/"
    publisher: "W3C Community Group"
  - title: "California Attorney General — Frequently Asked Questions: CCPA"
    url: "https://oag.ca.gov/privacy/ccpa"
    publisher: "California Office of the Attorney General"
  - title: "LGPD — Lei Geral de Proteção de Dados (Lei 13.709/2018)"
    url: "https://lgpd-brazil.info/"
    publisher: "Governo do Brasil"
  - title: "Colorado Privacy Act Rules — Universal Opt-Out Mechanism"
    url: "https://coag.gov/resources/colorado-privacy-act/"
    publisher: "Colorado Attorney General"
licence: CC-BY-4.0
---

# Global Privacy Control (GPC)

> Global Privacy Control is a browser-level signal that tells websites the user opts out of the sale or sharing of their personal data. California and Colorado legally require sites to honour it. Under the LGPD, it should be treated as a strong indicator of the user's privacy preference.

## What it is

Global Privacy Control (GPC) is a simple machine-readable signal that a user — through their browser, an extension, or a privacy-focused tool like DuckDuckGo or Brave — broadcasts to every site they visit. It says: *I do not want my personal data sold or shared.*

The signal is sent two ways:

- An HTTP request header: `Sec-GPC: 1`
- A JavaScript property: `navigator.globalPrivacyControl === true`

Both are read-only and trivial to detect server-side or client-side.

## Why it matters

### US enforcement

Under the California Consumer Privacy Act (CCPA/CPRA), the California Attorney General has confirmed that GPC is a valid opt-out signal that businesses must honour. Enforcement has followed — Sephora paid $1.2 million in 2022 for failing to process GPC signals. The Colorado Privacy Act explicitly lists GPC-style universal opt-out mechanisms as a required path from July 2024 onward. Connecticut and several other states have followed.

### LGPD relevance

The LGPD does not explicitly reference GPC, but the signal is highly relevant for Brazilian compliance:

- **LGPD is opt-in, not opt-out** — consent must be obtained before processing for non-essential purposes. GPC reinforces this by signalling the user has not consented.
- **Transparency and good faith** (Art. 6, I and VI) — ignoring a clear privacy signal while claiming to respect user preferences contradicts LGPD principles.
- **Consent revocation** (Art. 8, §5) — GPC can be treated as a universal revocation signal for previously granted consent to tracking/sharing.
- **ANPD enforcement priorities** — the 2026–2027 oversight map targets advertising-related data use. Honouring GPC demonstrates proactive compliance.

GPC does not replace cookie banners under EU or LGPD law, because both regimes are opt-in. But it is a strong privacy signal everywhere, and respecting it costs almost nothing.

## How to implement

Detect the signal on every request:

```js
// Client-side
if (navigator.globalPrivacyControl) {
  // user has opted out — do not load tracking
}
```

```python
# Server-side (any language)
if request.headers.get("Sec-GPC") == "1":
    opt_out = True
```

When the signal is present:

- **Do not sell or share** personal information as those terms are defined under the user's applicable law.
- **Do not load advertising trackers**, retargeting pixels, or data-broker tags.
- **Suppress consent-banner "accept" defaults** — the user has already expressed a preference. Under LGPD, treat GPC as equivalent to "reject all" for non-essential cookies.
- **Record the opt-out** as you would any other privacy request, including the timestamp and the URL.
- **Disclose your behaviour** in the privacy policy: state that you honour GPC and what that means in practice.

### Multi-jurisdiction approach

For sites serving EU, Brazilian, and US users, GPC should be treated as one of several inputs alongside cookie consent and any in-product privacy settings. The strictest preference wins:

| Jurisdiction | Consent model | GPC effect |
|---|---|---|
| EU/UK | Opt-in (ePrivacy + GDPR) | Treat as "reject all" — do not set non-essential cookies |
| Brazil (LGPD) | Opt-in (ANPD guidance) | Treat as "reject all" — do not set non-essential cookies |
| California (CCPA/CPRA) | Opt-out | Must honour as valid opt-out of sale/sharing |
| Colorado/Connecticut | Opt-out | Must honour as universal opt-out mechanism |

## Common mistakes

- Ignoring the signal entirely and continuing to load tracking scripts.
- Honouring GPC only for users who identify themselves as Californian. The signal does not include a state, and the AG has been clear: process it for any user who sends it.
- Treating GPC as a cookie banner replacement under GDPR or LGPD. Both are opt-in; GPC is opt-out. But GPC should suppress defaults and prevent non-essential tracking.
- Failing to mention GPC in the privacy policy, so users have no way to verify the site respects it.
- Not recording the GPC signal as part of the consent audit trail — the ANPD may request evidence of how privacy signals are processed.
