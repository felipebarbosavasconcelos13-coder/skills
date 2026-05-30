---
title: "Cookie consent"
category: privacy
status: required
updated: "2026-05-30T14:20:00.000Z"
sources:
  - title: "LGPD — Lei Geral de Proteção de Dados (Lei 13.709/2018)"
    url: "https://lgpd-brazil.info/"
    publisher: "Governo do Brasil"
  - title: "ANPD Guidance on Cookies and Trackers"
    url: "https://www.gov.br/anpd/pt-br"
    publisher: "ANPD"
  - title: "EDPB Guidelines 03/2022 on deceptive design patterns in social media"
    url: "https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-032022-deceptive-design-patterns-social-media_en"
    publisher: "EDPB"
  - title: "ePrivacy Directive 2002/58/EC, Article 5(3)"
    url: "https://eur-lex.europa.eu/legal-content/EN/TXT/?uri=CELEX:02002L0058-20091219"
    publisher: "EUR-Lex"
  - title: "CNIL — Cookies and other trackers"
    url: "https://www.cnil.fr/en/cookies-and-other-trackers"
    publisher: "CNIL"
licence: CC-BY-4.0
---

# Cookie consent

> Both the EU (ePrivacy + GDPR) and Brazil (LGPD) require opt-in consent before setting non-essential cookies. The ANPD has confirmed that LGPD principles apply to cookies and similar tracking technologies.

## What it is

Cookie consent is the requirement to obtain explicit, prior permission from users before storing or reading non-essential information on their device. This applies to cookies, localStorage, sessionStorage, IndexedDB, fingerprinting, and pixel trackers.

**Under EU law**, the ePrivacy Directive requires consent before storage/access, and the GDPR defines what valid consent looks like: freely given, specific, informed, and unambiguous, given by a clear affirmative action.

**Under Brazil's LGPD**, the ANPD's 2023 guidance confirmed that LGPD principles apply to cookies and tracking technologies. Consent (Art. 7, I) is the most straightforward legal basis for advertising and tracking cookies. The model is **opt-in** — non-essential cookies must not be set before the user actively accepts.

## Why it matters

Cookie consent is heavily enforced in both jurisdictions:

- **EU/UK**: CNIL, the Garante, the ICO, and the Belgian DPA issue fines regularly for pre-ticked boxes, hidden reject buttons, and banners that count scrolling as consent.
- **Brazil**: The ANPD's 2026–2027 enforcement priorities include monitoring targeted advertising practices and verifying "privacy by design and by default" safeguards. The ANPD suspended Meta's and X Corp's data processing in 2024 for non-compliant data use.

A non-compliant banner is also a poor user experience. Visitors do not want to negotiate with your site before reading it.

## How to implement

### Core principles (both EU and LGPD)

- **Strictly necessary cookies do not need consent.** Session cookies for login, shopping carts, security tokens, and load balancing are exempt. Analytics, advertising, social embeds, and A/B testing are not.
- **Set no non-essential cookies before the user accepts.** This includes Google Analytics, Meta Pixel, Hotjar, YouTube embeds, and most "marketing" tags.
- **Give "accept" and "reject" equal prominence.** Same size, same colour weight, same number of clicks. A bright green "Accept all" next to a grey "Manage preferences" is non-compliant under both regimes.
- **Rejecting must be as easy as accepting.** One click. Not a maze of toggles.
- **Reject means reject.** No tracking cookies, no fingerprinting fallback, no "legitimate interests" toggle that is on by default.
- **Be specific about purposes.** "Analytics" and "marketing" are categories users can choose between; "improving your experience" is not.
- **Let users change their mind.** A persistent link or icon in the footer to reopen the banner.
- **Dark patterns are prohibited.** Both EDPB guidelines and ANPD guidance explicitly ban deceptive design that steers users toward accepting.

### LGPD-specific requirements

- **Consent lifespan: 6 months.** After this period, re-request consent from users. Do not assume perpetual consent.
- **No pre-checked boxes.** LGPD requires active, affirmative consent — pre-ticked options are void (Art. 8, §4).
- **Purpose granularity.** Provide toggles per purpose category. Generic "I agree to all" without granular options is insufficient.
- **Language.** For Brazilian users, the banner and preference modal must be in **Portuguese**.
- **Children's data.** If your site may be accessed by minors, implement age-appropriate consent mechanisms. Parental consent is required for children's data under LGPD Art. 14.
- **Cookie walls are restricted.** Conditioning access to content on cookie acceptance undermines the "freely given" requirement.

### Required banner elements

**First layer (banner):**
- Concise purpose summary
- Accept All button
- Reject All button (or equivalent link with equal prominence)
- Manage Preferences link
- Link to Privacy Policy

**Second layer (preferences modal):**
- Granular purpose toggles (analytics, marketing, personalization, etc.)
- Legal basis per purpose (if applicable)
- List of specific cookies/technologies per category

**Persistent access:**
- Footer link or floating icon to reopen preferences at any time

### Consent record-keeping

Store the user's choice with:
- **Timestamp** (ISO 8601)
- **Choices made** (which purposes accepted/rejected)
- **Policy version** seen at time of consent
- **Jurisdiction detected** (for multi-region sites)

Under LGPD, maintain consent records for a minimum of **18 months**. The controller bears the burden of proving valid consent was obtained.

## Common mistakes

- Pre-ticked boxes for any non-essential purpose. Invalid under both GDPR (*Planet49*, 2019) and LGPD (Art. 8, §4).
- "By using this site you agree" — implied consent is not valid under either regime.
- Loading analytics scripts before the user has chosen.
- A "reject" button that is visually deprioritised, or only appears after clicking "preferences".
- No way to withdraw consent later.
- Not re-requesting consent after 6 months (LGPD requirement).
- Treating legitimate interest as a blanket alternative to consent for tracking cookies. The ANPD requires documented justification and the balancing test.
- Assuming the UK is exempt — UK GDPR and PECR are substantively the same as EU rules.
- Not providing the banner in Portuguese for Brazilian visitors.
- Failing to maintain timestamped consent records that can be produced for ANPD audits.
