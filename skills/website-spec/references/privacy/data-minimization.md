---
title: "Data minimisation"
category: privacy
status: recommended
updated: "2026-05-30T14:20:00.000Z"
sources:
  - title: "GDPR Article 5 — Principles relating to processing of personal data"
    url: "https://gdpr-info.eu/art-5-gdpr/"
    publisher: "EU GDPR"
  - title: "LGPD Article 6 — Principles (Necessity)"
    url: "https://lgpd-brazil.info/chapter_01/article_06"
    publisher: "Governo do Brasil"
  - title: "ANPD — Resolution CD/ANPD No. 15/2024 (Incident Notification)"
    url: "https://www.gov.br/anpd/pt-br"
    publisher: "ANPD"
  - title: "EDPB Guidelines 4/2019 on Article 25 Data Protection by Design and by Default"
    url: "https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-42019-article-25-data-protection-design-and_en"
    publisher: "EDPB"
licence: CC-BY-4.0
---

# Data minimisation

> Collect only the personal data you actually need for a specific purpose, keep it only as long as you need it, and redact it from anywhere it leaks unnecessarily. Under the LGPD, this is the "necessity" principle (Art. 6, III).

## What it is

Data minimisation is the principle that personal data must be "adequate, relevant and limited to what is necessary in relation to the purposes for which they are processed" — GDPR Article 5(1)(c).

Under Brazil's LGPD, the equivalent is the **necessity principle** (Art. 6, III): processing must be limited to the minimum necessary to achieve its purposes, covering only pertinent, proportional, and non-excessive data. This is reinforced by the **adequacy principle** (Art. 6, II): processing must be compatible with the stated purposes in the context of collection.

Both laws treat minimisation as a design constraint: do not collect a field unless you have decided in advance what you will do with it.

## Why it matters

Every personal data point you hold is a liability. It can be breached, subpoenaed, misused by an insider, or simply held longer than the law allows.

Under the LGPD, the consequences of over-collection are concrete:
- **Breach notification in 3 business days** — the more data you hold, the larger the incident scope and the harder it is to notify within the ANPD's tight deadline (Resolution 15/2024).
- **DSAR fulfillment in 15 days** — more data means more systems to search and more fields to redact.
- **Fines up to 2% of Brazilian revenue** (capped at R$50M per infraction) — the ANPD considers the volume of data involved when calculating sanctions.
- **Processing suspension** — the ANPD has demonstrated willingness to immediately suspend processing (Meta 2024, X Corp 2024) when data use exceeds what is necessary.

The cheapest way to protect data is not to collect it. The second cheapest is to delete it as soon as you no longer need it.

## How to implement

Apply minimisation at four points: collection, storage, logging, and retention.

### Collection

Walk through every form and every API. For each field, name the purpose. Drop anything that fails the test.

- A newsletter signup needs an email address. It does not need a name, a phone number, or a CPF.
- A contact form needs a way to reply. If you reply by email, you do not need a phone number.
- A delivery address is necessary to deliver a physical product. It is not necessary to download a PDF.
- Use a country dropdown instead of asking for a full address when only country matters for tax or compliance.
- **Do not collect CPF, RG, or other Brazilian identity documents** unless legally required for the specific transaction (e.g., invoicing under Brazilian tax law).

### Storage

- Separate identifiers from behavioural data where you can.
- Hash or tokenise where the raw value is not needed for the operation.
- For free-text fields, expect users to paste in personal data and plan accordingly.
- **Sensitive data** (LGPD Art. 11) — racial/ethnic origin, religious beliefs, political opinions, health, biometric, genetic data — requires explicit consent or a specific legal exception. If you do not need it, do not store it.

### Logging

Logs are the most common place where minimisation quietly fails.

- A request log that records full URLs will capture query-string tokens, search terms, and form data submitted over GET.
- Redact known sensitive fields, truncate URLs at the path, and never log request bodies in plain text in production.
- **Never log CPF, health data, or biometric identifiers.** Under LGPD, these are sensitive data with heightened protection requirements.
- Set log retention to the minimum operationally necessary. The ANPD's incident registry requirement (5 years) applies to security incidents, not to raw application logs.

### Retention

- Set a maximum retention period for each category of data and enforce it with a scheduled job, not a wiki page.
- Build deletion into the system from the start; retrofitting it after a request from the ANPD is painful.
- LGPD Art. 15 defines when processing must end: purpose achieved, period expired, data subject request, or ANPD determination.
- LGPD Art. 16 allows retention exceptions: legal/regulatory obligation, research (with anonymisation), transfer to third party (with legal basis), or exclusive use by the controller (anonymised).
- **Backups age out on their own schedule** — deletion needs to flow through to them too.

### Privacy by design (LGPD Art. 46)

The ANPD's 2026–2027 enforcement priorities include verifying "privacy by design and by default" safeguards. Implement:

- Data collection forms that start with zero optional fields and justify each addition.
- Default settings that minimise data exposure (e.g., profiles private by default).
- Automated retention enforcement — not manual processes.
- Regular data mapping reviews (quarterly recommended).

## Common mistakes

- Treating "we might want this later" as a purpose.
- Asking for CPF on a newsletter signup form.
- Asking for a phone number "in case we need to call" when you never call.
- Storing IP addresses in analytics or logs indefinitely.
- Logging request bodies, including login payloads, in production.
- A retention policy in the privacy notice that no system enforces.
- Collecting date of birth without an age requirement, insurance product, or legal obligation.
- Forgetting that backups age out on their own schedule — deletion needs to flow through to them too.
- Not conducting a Data Protection Impact Assessment (RIPD) when processing large volumes of personal data or sensitive data, as the ANPD may request one at any time (Art. 38).
