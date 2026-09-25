# Measurement, consoles and diagnostic flows

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 21. Measurement

| Need | Source |
|---|---|
| Classic search clicks, impressions, queries, pages | Each engine's webmaster console (Google Search Console etc., §A1). Up to 16 months of history in the main consoles |
| AI-answer visibility | Consoles now report it separately: **impressions** of links in AI answers at one engine; **citations**, grounding queries, cited pages and citation share at another (sampled; citations ≠ clicks) |
| Clicks from answer engines | Referral analytics. At least one answer engine appends `utm_source=<its domain>` to outbound links; segment by that and by referrer |
| Index status | Console page-indexing reports + URL inspection (live test) |
| Crawl health | Console crawl stats + **server logs** (verify bot IPs) |

- A drop in clicks doesn't always mean lost visibility. Check impressions and citations. [S2]
- Average position misleads; judge by impressions and clicks. [S1]
- No third-party tool has internal ranking or AI data. Treat third-party AI-visibility scores as estimates. [S1][S2]
- Before/after tests on stable, non-seasonal pages; wait weeks (§25.8). [S1]

**AI-visibility ladder** (a tracking convention [G]): eligible (crawlable and indexed) → retrieved → **cited** →
mentioned → recommended → converted. Also track "recommended against" and how the brand is framed.
- Console reports cover only the rungs they measure (impressions or citations).
- **Manual answer checks are samples, not rankings.** For each check, record:
  - query set, answer surface, date, locale, device, logged-in state;
  - citations shown;
  - repeat samples (answers vary run to run).
  - Report changes across samples, never a single answer as a position.

---

### 21.1 Consoles, removals and privacy
- **Register every host in every relevant engine's console** (§A1).
  - Domain-level properties verified by DNS cover all protocols and subdomains. Keep the verification token
    permanently: verification is re-checked, and losing it removes access.
  - Use more than one verification method.
  - Give consultants **read-only** access.
  - Data accrues from when the property is added. [S1][S2]
- **Removal tools** are temporary (§5.1). A permanent fix = delete / auth / `noindex`. Cover case and parameter
  variants of each URL. [S1][S2]
- **Redaction:**
  - Visual hiding (black boxes, tiny or white text, overlays) is **not** redaction; OCR, screen readers, change
    history and file metadata expose it.
  - Crop or flatten before publishing and strip metadata.
  - Publish the redacted version at a **new URL**, then request removal of the old one. [S1]
- **Never put personal data or secrets in URLs.** Robots-blocked URLs can still be indexed (without content), and
  fetched URLs may be retained by third parties. [S1][S4]
- **`site:` operator:** useful to spot hacked or spam pages (`site:example.com casino`). **Never use it to count
  indexed pages**; results are incomplete and unranked. Use the console's index reports. [S1]
- **Third-party SEO vendors and tools:**
  - Trust advice that cites official docs or labels itself as opinion.
  - Red flags:
    - guaranteed #1 rankings;
    - "priority submit" or a "special relationship" with an engine;
    - secret methods;
    - requiring links back to the vendor;
    - link schemes.
  - The site owner is responsible for the vendor's actions. [S1]

## 22. Diagnostic flows

**A. Page not indexed or not cited**
1. Console URL inspection: crawl allowed? Indexed? Canonical as intended?
2. robots.txt blocks the URL? CDN/WAF blocks the bots (403, 429, challenge)?
3. `noindex` in the HTML the bot received, or in `X-Robots-Tag`?
4. Status `200`? Soft 404? Login required?
5. Content and links present in the **raw** HTML (no JS)? Mobile parity?
6. Internal link and sitemap entry present? A new site with no inbound links needs weeks.
7. Technically clean but not indexed or cited → usually quality, duplication or weak answer-readiness (§14, §6).
   *Heuristic, not a vendor statement.*

**B. Traffic drop**
1. Rule out reporting anomalies (the console's data-anomaly notes).
2. Pattern:
   - impressions **and** clicks down → causes below;
   - impressions stable, clicks down → snippet or title weaker, or richer competitor results.
3. Causes:
   - **algorithm update:** small drops may reverse, so don't make radical changes; large broad drops → site-wide
     quality review, recovery takes weeks to months;
   - **technical:** site-wide issues act fast, a stray `noindex` acts slowly;
   - security issues;
   - manual actions;
   - seasonality (check trend data);
   - migration.
4. Split by query, page, country, device and search type. Sort pages by click difference to tell site-wide from
   section-level from page-level.
5. Check AI-answer reports separately. Clicks can fall while citations stay.

**C. Crawl problems**
1. Crawl stats and host availability in the consoles.
2. Server logs with verified bot IPs.
3. New pages take days at minimum. Freshness is often three days or more.
4. Fix capacity and wasted URLs. Better availability alone doesn't raise crawl demand.

**D. `noindex` page still showing** → not recrawled yet, or blocked by robots.txt. Request a recrawl; use removal
tools if urgent.

**E. Crawl budget** (only for sites with ~1M+ pages changing weekly, ~10k+ daily, or many "Discovered - currently not indexed"
URLs) [S1]
- Consolidate duplicates; block low-value URL spaces in robots.txt; `404`/`410` removed pages; fix soft 404s; avoid
  redirect chains; support `304`.
- Never use `noindex` to save budget: the page is still fetched.
- More budget comes only from more server capacity or more valuable content.
