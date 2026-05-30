---
title: "Privacy-respecting analytics"
category: privacy
status: recommended
updated: "2026-05-30T14:20:00.000Z"
sources:
  - title: "LGPD — Lei Geral de Proteção de Dados (Lei 13.709/2018)"
    url: "https://lgpd-brazil.info/"
    publisher: "Governo do Brasil"
  - title: "ANPD — Enforcement priorities 2026–2027"
    url: "https://www.gov.br/anpd/pt-br"
    publisher: "ANPD"
  - title: "ANPD Resolution CD/ANPD No. 32/2026 — Brazil-EU Mutual Adequacy Decision"
    url: "https://www.bakermckenzie.com/en/insight/publications/2026/01/brazil-and-european-union-mutual-data-protection-adequacy-decision"
    publisher: "Baker McKenzie"
  - title: "CNIL — Use of Google Analytics and data transfers to the United States"
    url: "https://www.cnil.fr/en/use-google-analytics-and-data-transfers-united-states-cnil-orders-website-manageroperator-comply"
    publisher: "CNIL"
  - title: "EDPB — 101 complaints concerning EU-U.S. data transfers (Google Analytics)"
    url: "https://edpb.europa.eu/news/news/2022/austrian-dpa-decision-101-complaints-issued_en"
    publisher: "EDPB"
licence: CC-BY-4.0
---

# Privacy-respecting analytics

> You can measure traffic without surveilling visitors. Aggregate, cookieless, locally-hosted analytics tools answer most product questions without the consent, transfer, and legal basis problems of ad-tech analytics — under both GDPR and LGPD.

## What it is

Privacy-respecting analytics measure what visitors do on your site without building a persistent profile of each visitor. The pattern: collect the smallest useful unit of data, aggregate it on the server, drop or hash anything that could identify a person, and keep it out of jurisdictions that introduce transfer problems.

Most product questions — what pages people read, where they came from, where they drop off — can be answered without cookies, fingerprints, or cross-site identifiers.

## Why it matters

### EU context

Between 2022 and 2023, the Austrian, French, Italian, and Danish data protection authorities ruled that the standard configuration of Google Analytics violated the GDPR because of transfers to the US. The EU–US Data Privacy Framework has changed the legal picture, but regulators still treat ad-tech-grade analytics with scrutiny.

### LGPD context

Under the LGPD, analytics that use cookies or device identifiers require a **legal basis** — typically consent (Art. 7, I) since the ANPD has confirmed the opt-in model for tracking technologies. Key considerations:

- **Consent is valid for only 6 months** — after that, you must re-request it. Cookieless analytics avoid this operational burden entirely.
- **International transfers** — Since January 2026, the Brazil-EU mutual adequacy decision (Resolution 32/2026) allows data to flow freely between Brazil and the EU. However, transfers to the US or other non-adequate countries still require ANPD-approved SCCs (Resolution 19/2024) or specific consent.
- **ANPD enforcement priorities** — the 2026–2027 oversight map explicitly targets monitoring of secondary uses of personal data for targeted advertising. Analytics that feed into ad targeting are under heightened scrutiny.
- **Data minimisation** (Art. 6, III) — collecting granular behavioural data beyond what is necessary for the stated analytics purpose violates the necessity principle.

A cookieless analytics tool does not require a consent banner under either EU or LGPD rules, because nothing is stored on the user's device. The data you get is also more representative, because nobody opts out.

## How to implement

The pattern is the same across tools:

- **Measure without cookies.** Use a daily-rotating hash of IP + user-agent + a site salt to estimate uniques. Drop the raw IP before it hits disk.
- **Aggregate at write time.** Store counts per page per day, not individual events with timestamps, unless you genuinely need them.
- **Anonymise the IP** at the edge if any IP is kept. The last octet of an IPv4 (or the last 80 bits of an IPv6) is enough to break linkability and still keep country-level geo.
- **Keep data in the relevant jurisdiction.** For Brazilian traffic, prefer analytics endpoints hosted in Brazil or the EU (covered by the adequacy decision). For other jurisdictions, ensure proper transfer mechanisms are in place.
- **Set a short retention.** Ninety days of daily aggregates answers most product questions. Raw events should be even shorter.
- **Document the legal basis.** If using cookieless analytics, document that no consent is needed because no data is stored on the user's device. If using cookie-based analytics, document consent as the legal basis and implement the 6-month renewal cycle.
- **Disclose in the privacy policy.** Name the analytics tool, the data collected, the retention period, the legal basis, and whether data is transferred internationally.

### Recommended approaches

Tools that follow this pattern include **Plausible**, **Fathom**, self-hosted **Matomo** (configured for IP anonymisation and no cookies), and **Cloudflare Web Analytics**. Listed as patterns, not endorsements — the specific tool matters less than the configuration.

If you must use ad-tech-grade analytics for marketing attribution:
- Isolate it behind explicit opt-in consent.
- Treat it as a separate system from product analytics.
- Implement the 6-month consent renewal for LGPD compliance.
- Document the legitimate interest assessment if not relying on consent (though consent is strongly recommended by the ANPD for tracking).
- Ensure the tool's data processing agreement covers LGPD obligations.

### LGPD-specific checklist for analytics

- [ ] Legal basis documented (consent or legitimate interest with balancing test)
- [ ] Consent banner implemented with opt-in model if cookies are used
- [ ] 6-month consent renewal mechanism in place
- [ ] IP anonymisation enabled before storage
- [ ] Data retention period defined and enforced
- [ ] International transfer mechanism documented (adequacy decision for EU, SCCs for others)
- [ ] Analytics tool named in privacy policy
- [ ] DPO/Encarregado aware of analytics data flows
- [ ] RIPD (impact assessment) prepared if large-scale profiling is involved

## Common mistakes

- Treating "we anonymise IPs" as enough while still loading a third-party script that fingerprints the browser.
- Using GA4 with default settings and assuming the EU–US DPF or Brazil-EU adequacy decision closes the case. Both authorities still expect documented assessment of the specific data flows.
- Running a "privacy-friendly" analytics tool *and* GA *and* Hotjar. Each one introduces its own consent and transfer questions.
- Keeping raw event logs forever because storage is cheap.
- Forgetting to disclose the analytics tool in the privacy policy.
- Not implementing the 6-month consent renewal for cookie-based analytics under LGPD.
- Using analytics data for purposes beyond what was disclosed (e.g., feeding into AI training or ad targeting without separate consent).
- Assuming "legitimate interest" is a blanket justification for analytics without conducting the three-part balancing test required by the ANPD.
