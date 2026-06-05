---
name: schema.md
description: Generate a complete, valid enterprise JSON-LD @graph for a regencyrecovery.com page (addiction treatment programs, substance-specific treatment, detox, withdrawal, levels of care, mental health, dual diagnosis, insurance, geo, emergency pages). Use whenever the user provides a page URL/topic/title/meta/H1 and asks for schema, JSON-LD, or structured data. Output is one ready-to-paste <script> tag, Google-valid, with stable @IDs. Pairs with wordpress.md and url.md.
---

# Rehab Schema Skill (regencyrecovery.com)

## Output rule
Output **one valid JSON-LD `<script type="application/ld+json">` block only** — no prose before or after. Single `@graph` array.

## Inputs needed (collect or infer)
Full page URL, page topic, SEO title, meta description, H1, primary + secondary keyword, page type (from url.md), datePublished/dateModified, approx word count, breadcrumb pillar.

## Business typing — the E-E-A-T decision (locked)
Regency Recovery & Wellness Center is a **real, licensed, single-location non-profit treatment facility** at **1301 E Gurley St, Prescott, AZ 86301**. This is the inverse of a referral network, so the typing is:

Use the **facility hybrid**: `Organization` + `MedicalClinic` (a subtype of both `LocalBusiness` and `MedicalOrganization`), joined by shared @IDs. **LocalBusiness/MedicalClinic IS correct and required here** — there is a verifiable physical address, so the local entity is a genuine trust signal, not spam. Include real NAP (name, address, phone) and geo on the `#medicalclinic` / `#place` nodes.

`MedicalClinic` is positioned as an actual treatment provider (Regency delivers care directly). It carries topical authority via `knowsAbout` AND legitimately provides the `Service`/`MedicalProcedure` nodes. The brand may state it treats addiction and co-occurring mental health conditions. Do not overclaim outcomes or guarantee sobriety.

> NAP consistency note to surface to the user: the address, phone, and business name in this schema must EXACTLY match Google Business Profile and all citations (capitalization, suite, abbreviations). Confirm the suite/street format against the live GBP before publishing — inconsistent NAP weakens local ranking. Verified address from the site's embedded map: 1301 E Gurley, Prescott, AZ 86301. Confirm whether there is a suite number.

> Ranking note: a named credentialed medical reviewer (Person → `reviewedBy` on the page) is a strong YMYL trust signal. If the user supplies a reviewer name + credential, add a `Person` node and reference it via `reviewedBy` on the `MedicalWebPage`. Omit cleanly if unknown rather than inventing one.

## Validity rules (prevent Rich Results errors)
- `medicalSpecialty` must use real schema.org `MedicalSpecialty` enum values. **Valid:** `Psychiatric`. Do NOT use "AddictionMedicine", "MentalHealth", "BehavioralHealth", "Addiction" — not in the enum; they throw warnings. Put those concepts in `knowsAbout` (free text) instead.
- No `aggregateRating`, `review`, or `Review` nodes unless the user supplies REAL, verifiable review data with a source. The homepage displays testimonials, but do not fabricate a numeric `aggregateRating`; reproducing testimonials as `Review` nodes requires real author attribution and is risky — omit by default and flag to the user as an available lever if they have genuine, sourced reviews.
- No recovery/sobriety guarantees, no "cure", no diagnosis language anywhere in text values.
- Avoid `Offer`/`price` on the Service node (a price:0 on treatment-adjacent service reads as "free treatment"). Describe the service without a priced Offer. Insurance acceptance may be expressed in prose-style description, not as a priced Offer.
- FAQ answers: medically cautious, encourage emergency care (911/988) where relevant, no diagnosis.
- All URLs absolute and real; only link pages that exist (cross-check url.md) and PRESERVE the site's nested paths.
- `inLanguage`: "en-US". Phone: "+1-928-460-7001".
- `addressCountry`: "US", `addressRegion`: "AZ", `addressLocality`: "Prescott", `postalCode`: "86301", `streetAddress`: "1301 E Gurley St" (confirm suite). Geo coordinates may be included for the Prescott location (approx lat 34.5400, lng -112.4538 — refine to the verified pin before publishing).

## FAQ rich-result caveat (set expectations)
Google restricts FAQ rich results to recognized authoritative health/gov sites (since 2023). Keep FAQPage markup regardless — Bing and AI engines (ChatGPT/Gemini/Perplexity) still parse it, and a licensed facility may qualify as a health entity — but don't promise FAQ star features in the SERP.

## Stable @IDs (site-level constant, page-level templated)
Site: `#organization`, `#medicalclinic`, `#website`, `#place`, `#contact`, `#logo`
Page: `{URL}#webpage`, `{URL}#article`, `{URL}#faq`, `{URL}#service`, `{URL}#breadcrumb`, `{URL}#terms`
Topic Things: `{URL}#primary-topic`, plus reusable `#medical-detox`, `#addiction-treatment`, `#dual-diagnosis`, `#withdrawal`, `#mat`, `#behavioral-health` (customize/select per page).

## @graph node checklist
1. **Organization** `#organization` — name "Regency Recovery & Wellness Center", url, telephone, logo (→ `#logo` ImageObject), contactPoint ref, nonprofit status may be noted, sameAs (real profiles only — GBP, Facebook, etc.; omit placeholders if unknown).
2. **MedicalClinic** `#medicalclinic` — `@type: ["MedicalClinic","LocalBusiness"]`; name, url, telephone, image, address (full PostalAddress), geo (`GeoCoordinates`), `medicalSpecialty: ["Psychiatric"]`; `knowsAbout: [...]` full topic list; `areaServed` (Prescott + Yavapai County + Arizona); `availableService` referencing the page `#service` and core programs; `openingHours`/`openingHoursSpecification` only if known (24/7 admissions line is `ContactPoint`, not necessarily clinic hours — do not invent clinical hours). Mark as a real treatment provider.
3. **ContactPoint** `#contact` — telephone "+1-928-460-7001", contactType "admissions" (or "customer service"), areaServed US, availableLanguage English, hoursAvailable 24/7 for the helpline if accurate.
4. **Place** `#place` — full PostalAddress (street, locality, region, postalCode, country) + GeoCoordinates. This is real; populate it fully (the key difference from a referral-network schema).
5. **WebSite** `#website` — publisher → Organization; SearchAction potentialAction.
6. **MedicalWebPage** `{URL}#webpage` — use `MedicalWebPage` (not plain WebPage) for clinical/program/substance/mental-health pages; isPartOf website; about → #primary-topic; mainEntity → article/service/faq; mentions → topic Things; significantLink → real internal links from the map (nested paths); inLanguage; `reviewedBy` → Person if a reviewer is supplied; `lastReviewed` date. Use plain `WebPage` only for non-clinical pages (contact, donate, blog index).
7. **Article / MedicalScholarlyArticle** `{URL}#article` — for editorial/blog content use `Article`; headline (H1), description (meta), author + publisher → Organization, mainEntityOfPage → webpage, datePublished/dateModified, articleSection, keywords, wordCount, inLanguage. Omit on pure service/program landing pages if a MedicalWebPage + Service already carry the content.
8. **Service / MedicalProcedure** `{URL}#service` — page-specific. For treatment programs use `MedicalProcedure` or `MedicalTherapy` where it fits (e.g. detox→`MedicalProcedure` "Medically Supervised Drug & Alcohol Detox"; MAT→`MedicalTherapy`; therapy pages→`MedicalTherapy` "Cognitive Behavioral Therapy"); for navigational/insurance pages use `Service`. Name examples: detox→"Medically Supervised Detox"; residential→"Residential Addiction Treatment"; PHP→"Partial Hospitalization Program"; IOP→"Intensive Outpatient Program"; substance page→"[Substance] Addiction Treatment"; mental-health→"[Condition] Treatment Program"; insurance→"Insurance Verification & Admissions". `provider` → `#medicalclinic` (NOT a referral org — Regency provides it directly); areaServed Prescott/AZ; audience; NO priced Offer.
9. **BreadcrumbList** `{URL}#breadcrumb` — Home › [pillar by page type] › [Page], mirroring the NESTED URL. Pillars: Rehab Programs (`/arizona-rehab-programs/`) / Addiction Treatment (`/arizona-addiction-treatment-programs/`) / Substance Abuse (`/arizona-substance-abuse-addiction-treatment-center/`) / Mental Health Treatment (`/arizona-mental-health-treatment-centers/`). For a therapy sub-page, breadcrumb includes the therapy-services parent too (4 levels). Use real pillar URLs from the map.
10. **FAQPage** `{URL}#faq` — 8–12 Question/acceptedAnswer pairs, pulled from the page's actual FAQ; medically cautious; reference 911/988 where relevant.
11. **DefinedTermSet** `{URL}#terms` + **DefinedTerm** children — the page's clinical entities (medical detox, withdrawal, dual diagnosis, MAT, CIWA-Ar, COWS, PHP, IOP, residential treatment, etc.) as a glossary set.
12. **Thing / MedicalCondition / DrugClass entities** — `#primary-topic` + the mentioned topic Things, each with name + description, referenced by MedicalWebPage about/mentions. For substance pages, the substance/condition may be typed `MedicalCondition` (e.g. "Opioid Use Disorder") for stronger entity association; keep descriptions non-diagnostic.

## Cross-checks before output
- Page type matches breadcrumb pillar, webpage type (MedicalWebPage vs WebPage), and Service/MedicalProcedure name.
- Every significantLink/breadcrumb URL exists in url.md and preserves nested paths.
- LocalBusiness/MedicalClinic present WITH real NAP + geo (this is required here, unlike a referral network). NAP matches GBP exactly.
- No fabricated ratings/reviews, no priced treatment offer, no invalid medicalSpecialty, no outcome/cure guarantees, no diagnosis language.
- `provider` on services points to `#medicalclinic` (Regency provides care directly), never to a referral entity.
- Dates, word count, keywords filled (not left as placeholders).
- Valid JSON (no trailing commas), single graph, single script tag.
