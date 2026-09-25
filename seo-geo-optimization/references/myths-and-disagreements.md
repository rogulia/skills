# Myths and engine disagreements

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 23. Ignored or ineffective (myths)

| Claim | Status |
|---|---|
| Meta keywords | Not used [S1][S2] |
| Sitemap `priority` / `changefreq` | Ignored [S1][S2] |
| `noindex` in robots.txt | Not supported [S1] |
| llms.txt / AI text files / special AI schema | Not documented as used; ignored by at least one engine [S1] |
| "Chunk" pages into tiny pieces | Not required; no ideal length [S1] (write self-contained sections instead, §14.2) |
| Rewriting for AI / covering every long-tail phrasing | Unnecessary; engines understand synonyms [S1] |
| Buying "mentions" | "isn't as helpful as it might seem"; spam systems apply [S1][S2] |
| Word count targets | No preferred length [S1] |
| Trust/expertise frameworks as a direct factor | Not a single ranking factor; approximated by many signals [S1] |
| Duplicate-content penalty | None by itself [S1][S2] |
| Keywords in the domain / URL | "Hardly any effect beyond appearing in breadcrumbs"; exact-match domains get limited credit [S1] |
| TLD (except ccTLD for country) | Low-impact signal [S1] |
| Heading order or count, perfect HTML validity | Don't matter [S1] |
| HTTP/2 | No ranking boost [S1] |
| Fake freshness | Doesn't help; red flag [S1][S2] |
| Paying the engine / ad spend | No organic or AI effect [S1][S2][S3] |
| Rater or third-party "scores" | Not ranking inputs [S1] |
| `rel=next/prev`, HTML `lang` for detection, geo meta | Not used [S1] |
| Resubmitting unchanged URLs or sitemaps | Doesn't speed indexing [S1][S2] |

**Common folklore in public checklists — no engine documents it** (reject as rules [G survey]):
- **Length targets:**
  - fixed character limits for titles (50–60) or descriptions (120–160); engines state no limit;
  - minimum word counts per page type;
  - fixed "40–60-word answer blocks".
- **Keyword placement:** keyword-in-slug or first-100-words requirements; keyword density.
- **"Exactly one H1"** as a hard rule.
- **Markup and files "for AI":** FAQ/HowTo schema; `ai.txt`, `/ai/*.json`, `noai` meta; llms.txt as a citation lever.
- **Freshness:** fixed refresh cadences ("update every 90 days").
- **Correlation statistics** ("X× more citations if …") and lab-study percentages presented as engine rules.
- **Wrong bot mapping:** confusing training bots with AI-search bots (e.g. assuming the training bot controls
  AI-answer visibility). Always check §A1.

## 24. Known disagreements between engines — how to act

| Topic | What differs | Action |
|---|---|---|
| Crawl-delay | Honoured by some, ignored by others | Use `503`/`429` for emergencies; crawl-delay only by owner decision |
| Chunked formatting | One engine: no chunking needed. Another: assistants split pages and lift Q&A verbatim | Self-contained sections with direct answers; no page splitting |
| Hidden-by-default content | Fine for one engine; may be skipped by another's AI answers | Core facts visible by default |
| `noarchive` / `nocache` | Active AI/training controls at one engine; unused elsewhere | Set only by owner decision; they reduce AI citations there |
| robots.txt as a results lever | Crawl-only for most; one answer engine treats disallow as exclusion | Use `noindex` for index control, plus per-engine bot rules for AI decisions |
| `noindex` reach | Direct at search engines; via search partners at an answer engine | Same tag; allow the page to be crawled |
| User-triggered fetchers | Ignore robots.txt at some engines; honour it at others | Don't rely on robots.txt to stop user-initiated reads |
| Temporary redirects | Weak signal at one; "less than 2 days" at another | 302 only for very short changes |
| Link directories and reciprocal links | Tolerated by one; spam at another | Follow the stricter rule (§15) |
| JS rendering | A full renderer at some; none at some fetchers | Server-render |
| Removal tools | ~6 months vs 90 days (extendable) | Re-check expiry; fix permanently |
| Training opt-out mechanism | Crawler vs control token vs meta directive | Configure per engine (§A1) |
| Change notification | IndexNow at some engines; not at others | IndexNow plus sitemaps |
