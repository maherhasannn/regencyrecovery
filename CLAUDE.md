# Regency Recovery — Claude Code Instructions

## Project overview

This repo powers content operations for [regencyrecovery.com](https://regencyrecovery.com) — Regency Recovery & Wellness Center, a **licensed non-profit addiction treatment and mental health facility** physically located in **Prescott, Arizona**. The facility provides medical detox, residential treatment, PHP, IOP, outpatient care, medication-assisted treatment (MAT), addiction therapy (CBT/DBT/holistic), structured sober living, aftercare, men's and women's tracks, dual diagnosis, and mental health treatment (anxiety, depression, PTSD). The site runs on WordPress.

All content targets people in crisis or actively researching help: panicking spouses/parents, someone in active withdrawal, a person deciding to get treatment, families researching a loved one's substance or condition, and high-intent searchers who need help today. Every article is written for one conversion goal — phone calls to **(928) 460-7001** — with the contact form as the secondary CTA and `/donate/` as the mission/tertiary CTA (Regency is a non-profit).

**Positioning guardrail (non-negotiable):** Regency Recovery is a **real, licensed, single-location treatment facility** — it owns and operates its own programs and admits clients directly. The active, ownership voice ("we treat," "our facility," "our clinical team," "our Prescott center," "we verify your benefits," "available 24/7") is legitimate and expected. There is NO "we are not a facility" disclaimer. The two guardrails that keep this defensible: (1) never invent a SECOND physical location — there is one facility, in Prescott; other-town geo pages say "our Prescott facility serving [area]," and (2) never guarantee treatment outcomes, sobriety, a "cure," coverage outcomes, in-network status, or out-of-pocket figures. This framing must hold across every article and the schema.

Canonical model pages for structure, depth, and voice: the live program pages, especially `https://regencyrecovery.com/arizona-addiction-treatment-programs/partial-hospitalization-program-prescott-az/` and `https://regencyrecovery.com/arizona-addiction-treatment-programs/drug-detox-treatment-prescott-az/`.

---

## Environment

WordPress credentials are loaded automatically from `.env` at session start via the SessionStart hook. They are available as environment variables:

- `WP_USERNAME` — WordPress admin username
- `WP_APP_PASSWORD` — WordPress application password
- `WP_API_ENDPOINT` — `https://regencyrecovery.com/wp-json/wp/v2/posts`  *(confirm — WP default assumed)*

If credentials are missing, run the session-start hook manually:
```bash
CLAUDE_CODE_REMOTE=true CLAUDE_PROJECT_DIR=$(pwd) CLAUDE_ENV_FILE=/tmp/claude-env bash .claude/hooks/session-start.sh
```

---

## Available skills

Three custom skills are installed automatically at session start:

| Skill | Purpose |
|---|---|
| `/regency-url-map` | Canonical URL map (`url.md`) — existing live pages across the Rehab Programs, Addiction Treatment, Substance Abuse, and Mental Health pillars (using the site's NESTED path convention) plus planned expansion pages (geo, withdrawal-education blog, insurance, audience). Always pull slugs, page type, parent pillar, geo, keyword, and CTA mode from here. |
| `/regency-content` | Full article engine (`wordpress.md`) — deep-clinical 4,000–6,000+ words, 3 embedded CTA boxes (top/middle/end), 15–25 FAQs, comparison table, crisis blockquotes, medically-reviewed byline, trusted-sources list, editorial disclaimer. |
| `/regency-schema` | Generates the JSON-LD `<script>` block (`schema.md`). Requires the article content as input. |

---

## Full content + posting routine

Follow these steps in order every time a new draft is created.

### Step 1 — Pick a slug with `/regency-url-map`

Invoke `/regency-url-map` to review the canonical page map. Either:
- Pick an existing slug that needs to be built or refreshed (existing live pages are marked 🔁 — they predate this pipeline and benefit from a rewrite), or
- Pick a planned expansion slug (marked blank), or
- Identify the right cluster and mint a new slug following that cluster's NESTED naming convention (substances under `/arizona-substance-abuse-addiction-treatment-center/`, levels of care under `/arizona-addiction-treatment-programs/`, mental health under `/arizona-mental-health-treatment-centers/`, etc.)

The map gives the slug its **page type** (program / substance / mental-health / education / emergency-intent / geo / comparison / audience), **cluster + parent pillar** (drives internal linking), **geo**, **target keyword**, and **CTA mode** (call-primary or education-primary). The slug drives the page URL and all internal linking. Never invent a slug without checking here first, and always preserve the nested path structure.

### Step 2 — Write the article with `/regency-content`

Invoke `/regency-content` with the chosen slug/topic. The skill will produce:

- Deep-clinical 4,000–6,000+ word article in WordPress Gutenberg block HTML (emergency-intent pages run shorter — see map)
- Clinical depth: named assessment scales (CIWA-Ar, COWS), hour/day withdrawal timelines, specific medication classes, substance-by-substance danger gradients, levels-of-care framing (ASAM), and matched depth on mental-health pages (modalities, dual-diagnosis rationale)
- 3 embedded CTA boxes, contextually rewritten per page (Box 1 dark navy hero + phone; Box 2 white clinical-assessment card + pillar/insurance links; Box 3 light continuum card + next-stage link)
- Medically-reviewed byline + top and closing crisis blockquotes (911 / 988 / Arizona crisis line + 928 number)
- 15–25 FAQ entries formatted for AI Overview extraction
- At least one comparison table (inpatient vs outpatient, levels of care, or detox vs rehab)
- Internal links using only confirmed slugs from `/regency-url-map`, preserving nested paths (up to parent pillar, sideways to cluster siblings + related pages, plus contact + the relevant detox/crisis page)
- External links to authoritative sources only: SAMHSA, NIDA, CDC, NIH, NIAAA, Arizona Department of Health Services / AHCCCS, 988 Lifeline
- Editorial disclaimer ("licensed treatment facility in Prescott, AZ"; educational, not medical advice; admission depends on clinical assessment, availability, and benefit verification) + full deliverables block at the end (SEO title, meta, slug, H1, internal link map, etc.)

**Before moving to Step 3:** Verify every link in the article — internal and external — returns a valid response. Confirm no `[BRACKETED PLACEHOLDER]` text survives in the CTA boxes (topic, geo, headlines, hrefs all customized to this page). Do not skip this check.

### Step 3 — Generate schema with `/regency-schema`

Invoke `/regency-schema` and pass:
1. The full page URL (`https://regencyrecovery.com/<nested-path>/<slug>/`)
2. The complete article content from Step 2

The skill outputs a single `<script type="application/ld+json">` block — ready to paste into WordPress as a Custom HTML block at the bottom of the post content.

The schema includes: `Organization`, `MedicalClinic` (typed as both `MedicalClinic` and `LocalBusiness`, a REAL treatment provider with full NAP + geo; `knowsAbout` topics, `medicalSpecialty: ["Psychiatric"]` only — no invalid enum values), `ContactPoint`, `Place` (full real address), `WebSite`, `MedicalWebPage` (or `WebPage` for non-clinical pages), `Article` (editorial/blog only), `FAQPage`, `Service`/`MedicalProcedure`/`MedicalTherapy` (provider → the facility, no priced Offer), `BreadcrumbList` (nested), `DefinedTermSet`, and relevant `DefinedTerm` / `Thing` / `MedicalCondition` entities for the page's cluster.

**`LocalBusiness`/`MedicalClinic` IS required here** — Regency is a real single-location facility with a verifiable address (1301 E Gurley, Prescott, AZ 86301), so the local entity is a genuine trust signal, not spam. Service `provider` always points to the facility (`#medicalclinic`), never to a referral entity. No `aggregateRating` or review nodes unless real, sourced review data is supplied.

**The schema skill will refuse to generate if:**
- No FAQ section exists in the article
- Any link hasn't been verified against `references/valid-links.md`

### Step 4 — Final link check

Before posting, confirm:
- [ ] All internal links resolve to real regencyrecovery.com pages with correct NESTED paths (use the valid-links list in `.claude/skills/regency-schema/references/valid-links.md`)
- [ ] All external links return HTTP 200
- [ ] No bracketed placeholder text survives in the CTAs (`[EMOTIONAL HOOK HEADLINE]`, `[CLUSTER PILLAR URL]`, `[NEXT-STAGE URL]`, etc.)
- [ ] Phone links point to `tel:9284607001` (displayed "(928) 460-7001") — displayed number and tel: target identical on every box
- [ ] No staging-domain links survive (the live site has legacy CTAs pointing to `kevinl132.sg-host.com` — replace any with the production `regencyrecovery.com` domain)
- [ ] Schema JSON is valid (no trailing commas, all brackets matched), and NAP matches Google Business Profile exactly

### Step 5 — Post to WordPress

Post the draft using the WordPress REST API:

```bash
python3 post_draft.py
```

The script reads credentials from `.env`, posts to `https://regencyrecovery.com/wp-json/wp/v2/posts` with `status: draft`, and prints the post ID and preview URL on success.

**The content field must include:**
1. The full Gutenberg block HTML from Step 2 (with the 3 CTA boxes already embedded)
2. The `<script type="application/ld+json">` block from Step 3 appended as a `<!-- wp:html -->` block at the bottom

**On success:** Log the post ID. The draft is live in WordPress and ready for review before publishing.

---

## Content standards

- Voice: senior addiction-medicine-literate clinician at a real, intimate non-profit facility speaking to a panicking partner or a person ready for help — specific, calm, clinically credible, warm, never sensational
- Active ownership voice ("we treat," "our facility," "our clinical team," "we verify your benefits," "24/7") — legitimate because Regency is the actual provider; never invent a second location
- Every section opens with a 2–3 sentence direct answer (AI Overview ready)
- Deep clinical register required — named scales, timelines, medications, substance-specific danger gradients, levels of care (see `/regency-content`)
- No unsupported guarantees, no "best" or "top rated" as ranked claims, no recovery/outcome/sobriety/cure promises
- Medical framing: "can," "may," "in many cases," "depending on your plan/assessment" — never "will" or "guaranteed"
- Coverage always framed as verified before admission, never guaranteed; outcomes framed as evidence-based and individualized
- 911 / 988 callouts wherever overdose or severe withdrawal is discussed
- Word count: 4,000–6,000+ words per geo/pillar/program article (emergency-intent pages shorter)
- CTAs: 3 per article, all contextually rewritten — no generic copy, no surviving placeholders

## NAP / brand constants (never change)

- **Name:** Regency Recovery & Wellness Center
- **Phone:** (928) 460-7001 / `+1-928-460-7001` / `tel:9284607001`
- **Address:** 1301 E Gurley St, Prescott, AZ 86301  *(verified from the site's embedded contact map; confirm exact street format / suite number against Google Business Profile before relying on it in schema)*
- **Contact URL:** `https://regencyrecovery.com/contact-us/`
- **Donate URL:** `https://regencyrecovery.com/donate/`
- **Pillars:** Rehab Programs `https://regencyrecovery.com/arizona-rehab-programs/` · Addiction Treatment `https://regencyrecovery.com/arizona-addiction-treatment-programs/` · Substance Abuse `https://regencyrecovery.com/arizona-substance-abuse-addiction-treatment-center/` · Mental Health `https://regencyrecovery.com/arizona-mental-health-treatment-centers/`
- **API endpoint:** `https://regencyrecovery.com/wp-json/wp/v2/posts`  *(confirm)*
- **Known issue:** Several live pages contain legacy CTA links to the staging domain `kevinl132.sg-host.com` (e.g. /donate/, /contact-us/). Always replace these with the production `regencyrecovery.com` domain in new and rewritten content.
