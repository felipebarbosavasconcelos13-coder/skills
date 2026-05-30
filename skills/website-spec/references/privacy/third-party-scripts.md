---
title: "Third-party scripts and privacy"
category: privacy
status: recommended
updated: "2026-05-30T14:20:00.000Z"
sources:
  - title: "LGPD — Lei Geral de Proteção de Dados (Lei 13.709/2018)"
    url: "https://lgpd-brazil.info/"
    publisher: "Governo do Brasil"
  - title: "ANPD — Technology Radar No. 3 (Generative AI)"
    url: "https://www.gov.br/anpd/pt-br/centrais-de-conteudo/documentos-tecnicos-orientativos/radar_tecnologico_ia_generativa_anpd.pdf"
    publisher: "ANPD"
  - title: "EDPB Guidelines 2/2023 on Technical Scope of Art. 5(3) ePrivacy Directive"
    url: "https://edpb.europa.eu/our-work-tools/our-documents/guidelines/guidelines-22023-technical-scope-art-53-eprivacy-directive_en"
    publisher: "EDPB"
  - title: "MDN — Content Security Policy (CSP)"
    url: "https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP"
    publisher: "MDN"
  - title: "MDN — Subresource Integrity"
    url: "https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity"
    publisher: "MDN"
licence: CC-BY-4.0
---

# Third-party scripts and privacy

> Every script loaded from another domain can read cookies, see the URL, and exfiltrate data from your page. Under both GDPR and LGPD, you are responsible for the data processing that third-party scripts perform on your site.

## What it is

A third-party script is any `<script src="…">` whose source is not your own origin. Once it loads, it runs with full access to the page: the DOM, cookies that are not `HttpOnly`, localStorage, the URL, the referrer, form inputs as the user types, and any data your own JavaScript exposes.

The browser also tells the third party about the visit at the network layer: IP address, user-agent, Accept-Language, the page that loaded the script (Referer), and any cookies that domain has set.

## Why it matters

### Security risk

Third-party scripts are the largest source of unintended data leaks on the modern web. A single tag for a chat widget, A/B tester, fonts service, or "session replay" tool can ship every URL a logged-in user visits to a third party. *Magecart*-style supply-chain attacks have hit British Airways, Ticketmaster, and many smaller sites.

### LGPD compliance risk

Under the LGPD, the **controller** (controlador) is responsible for all personal data processing that occurs through their website — including processing performed by third-party scripts:

- **Joint controllership** — if a third-party script collects data for its own purposes (e.g., Meta Pixel building ad profiles), you may be a joint controller with that third party. Both parties are liable.
- **Processor obligations** — if the third party processes data only on your instructions (e.g., a self-hosted analytics tool), you must have a data processing agreement covering LGPD obligations (Art. 39).
- **Legal basis required** — every third-party script that processes personal data needs a documented legal basis. For tracking scripts, this is typically consent (opt-in).
- **International transfers** — if the script sends data to servers outside Brazil, you must ensure proper transfer mechanisms: adequacy decision (EU since Jan 2026), ANPD-approved SCCs (Resolution 19/2024), or specific consent.
- **Incident liability** — if a third-party script is compromised and leaks user data, you must notify the ANPD within **3 business days** and affected data subjects within the same timeframe. "It was the vendor's fault" is not a defence.
- **Children's data** — the ANPD's 2026–2027 priorities and the ECA Digital impose heightened obligations for platforms accessible to minors. Third-party scripts that profile children (e.g., ad trackers on educational content) create severe compliance risk.

### EU/ePrivacy risk

The EDPB has confirmed that the ePrivacy Directive applies to any technology that reads or writes to the user's device, not just cookies — so a tracking pixel without a cookie is still in scope.

## How to implement

Treat every third party as a liability you are choosing to take on.

### Audit and justify

- **Audit what you have.** Use the network panel, or a tool like Request Map, to list every domain your pages contact. Most sites are surprised.
- **Justify each one.** What does it do, who owns it, what data does it receive, and what is the business case for keeping it? Anything that fails this should be removed.
- **Map to legal basis.** For each third-party script, document: what personal data it accesses, the legal basis under LGPD Art. 7, whether it requires consent, and whether it transfers data internationally.
- **Classify by necessity.** Strictly necessary (load immediately), functional (load after consent for that category), or marketing/analytics (load only after explicit opt-in consent).

### Technical controls

- **Self-host where you can.** Fonts, JavaScript libraries, and icon sets rarely need to come from a CDN. Self-hosting eliminates a third-party contact entirely and is often faster.
- **Defer or gate the rest.** Anything that is not essential to first render should load after user interaction, or only after consent for non-essential storage.
- **Use a Content Security Policy.** A `script-src` allowlist prevents a compromised page from loading scripts you did not approve. See `security/content-security-policy`.
- **Use Subresource Integrity** for any script you must load from a third party where the URL is stable. An SRI hash ensures the file has not changed since you audited it.
- **Set a Referrer-Policy** of `strict-origin-when-cross-origin` or stricter so third parties do not see full URLs.
- **Implement consent-gated loading.** Use your CMP to conditionally inject scripts only after the user has consented to the relevant purpose category.

### Vendor management (LGPD compliance)

- **Data Processing Agreements** — execute contracts with all third-party vendors that process personal data on your behalf. Include LGPD-specific clauses: Art. 39 obligations, breach notification within 3 business days, data subject request assistance, and deletion on termination.
- **Transfer Impact Assessments** — for vendors outside Brazil/EU, document the transfer mechanism and assess the adequacy of protection in the destination country.
- **Regular review** — audit third-party scripts quarterly. Vendors change their data practices, add sub-processors, or get acquired. Your compliance posture must track these changes.
- **AI and generative AI** — the ANPD's Technology Radar No. 3 (2024) warns that AI tools embedded as third-party scripts may use visitor data for model training. Verify that no third-party script feeds user interactions into AI training without explicit, specific consent.

## Common mistakes

- Loading dozens of marketing tags through a tag manager and treating the tag manager as the audit.
- Hot-linking fonts or libraries from a public CDN to "save bandwidth" while leaking visitor IPs to that CDN.
- Adding session-replay tools without checking what they record. Many capture passwords and credit cards by default.
- Setting a permissive CSP (`script-src *`) that defeats the protection.
- Auditing once at launch and never again.
- Not having Data Processing Agreements with third-party script vendors — leaving you exposed if the ANPD requests documentation.
- Assuming the Brazil-EU adequacy decision covers all third-party transfers. It only covers EU/EEA — US-based vendors still require SCCs or consent.
- Embedding AI chatbots or assistants without verifying whether they process/store conversation data containing personal information.
- Not gating third-party scripts behind consent for Brazilian users, where the opt-in model applies.
