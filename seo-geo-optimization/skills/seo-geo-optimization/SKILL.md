---
name: seo-geo-optimization
description: Evidence-based SEO and GEO (generative engine optimization) rules for classic search engines and AI answer engines, built only from official vendor documentation, with an audit procedure. Use when auditing or fixing a website's crawlability, robots.txt, AI crawler access (search vs training vs user-triggered bots), indexing and noindex, canonicals, redirects and site migrations, sitemaps and IndexNow, JavaScript rendering, mobile parity, Core Web Vitals, hreflang, structured data, titles and snippets, spam risks, content quality and answer-ready writing, AI-answer visibility and citations, llms.txt questions, or search and AI-answer measurement.
license: CC-BY-4.0
metadata:
  author: Iurii Rogulia
  source: https://iurii.rogulia.fi
  version: "1.0.0"
  verified: "2026-09-25"
---

# SEO & GEO optimization

Rules for being crawled, indexed, ranked and **cited** by classic search engines and by generative AI answer engines.
The rules are **engine-neutral**: they apply to both kinds of engine.

**Basis.** Only official first-party documentation of the major search and AI answer engines, fetched **2026-09-25**
(≈190 pages, see `references/sources.md`). Nothing comes from memory or SEO folklore. Known folklore is listed as
rejected in §23.

**Tags.**
- `[S1]`–`[S4]` = which vendor documentation set states the rule.
- `[P]` = an open protocol, web standard or public tool doc.
- `[G]` = a process convention from public audit skills (no engine claim).
- Two or more `[S]` tags = stated independently by several engines. One tag = one engine only; the rule still applies
  because following it doesn't hurt elsewhere ("build for the strictest engine").

**Naming.** The rules don't name engines, except Google Search Console and Lighthouse as tool examples. Crawler
tokens, consoles and endpoints needed for configuration are in `references/engine-reference.md` (§A1).

## Where the rest lives

Section numbers (§) are shared across files. Read only the file the task needs.

| § | Topic | File |
|---|---|---|
| 3–4 | Bot classes, reachability, bot verification, crawl limits, crawl rate, robots.txt | [references/bots-and-crawling.md](references/bots-and-crawling.md) |
| 5 | `noindex`, robots meta / `X-Robots-Tag`, `data-nosnippet`, AI-answer and training controls, meta tags | [references/index-snippet-ai-controls.md](references/index-snippet-ai-controls.md) |
| 6–8 | Canonicals and duplicates, redirects, site moves, URLs, internal and outbound links | [references/canonical-redirects-urls-links.md](references/canonical-redirects-urls-links.md) |
| 9–12 | Sitemaps, IndexNow, JavaScript rendering, lazy loading, mobile parity, Core Web Vitals, interstitials | [references/sitemaps-rendering-mobile-performance.md](references/sitemaps-rendering-mobile-performance.md) |
| 13 | hreflang, multilingual and multi-regional sites | [references/international.md](references/international.md) |
| 14 | Content quality, answer-ready page structure, freshness, AI-generated content, reviews | [references/content.md](references/content.md) |
| 15 | Spam and manipulation policies (incl. prompt injection, scaled content) | [references/spam-policies.md](references/spam-policies.md) |
| 16–17 | Titles, snippets, answer boxes, site name, favicon, images, video, structured data | [references/appearance-and-structured-data.md](references/appearance-and-structured-data.md) |
| 18–20 | Commerce feeds, local, browser agents, llms.txt | [references/commerce-agents-llms-txt.md](references/commerce-agents-llms-txt.md) |
| 21–22 | Measurement, AI-visibility tracking, consoles, removals, privacy, diagnostic flows | [references/measurement-and-diagnostics.md](references/measurement-and-diagnostics.md) |
| 23–24 | Myths and folklore; where engines disagree and how to act | [references/myths-and-disagreements.md](references/myths-and-disagreements.md) |
| 25 | Audit modes, evidence rules, finding format, change lifecycle, regression guard | [references/audit-procedure.md](references/audit-procedure.md) |
| A1 | Engine reference: crawler tokens, verification endpoints, consoles | [references/engine-reference.md](references/engine-reference.md) |
| A2 | Sources and staleness notes | [references/sources.md](references/sources.md) |

**For an audit:** start with §25, then work through §3 → §5 → §6 → §9–12 → §14–17.

## 0. How to use this skill

- **Precedence:**
  1. the project's own docs and owner decisions;
  2. this skill;
  3. the live vendor doc, if its "last updated" date is newer than 2026-09-25.
- **Design rule:** when engines publish different limits or behaviours, meet the strictest one. The text states the
  strict value; §24 lists the known disagreements.
- **Silence ≠ agreement.** If no engine states something, it isn't a rule here. Never fill a gap from memory; say
  "not documented".
- **Owner decisions, never agent decisions:**
  - opting out of AI answers or model training;
  - blocking or allowing a class of bots;
  - buying or joining a commerce/feed programme;
  - anything that changes public claims (prices, facts, reviews).

## 1. How search and answer engines work — what agents need to know

- **Pipeline:** discover → crawl → render → index → serve. Each stage can drop a URL. There is no guarantee of
  crawling, indexing, showing or citing, even with full compliance. [S1][S2]
- **AI answers run on the search index.** Generative answers are grounded in pages retrieved from the same index,
  crawlers and quality systems as classic results (retrieval-augmented generation, "grounding"). Optimizing for AI
  answers is optimizing for search. [S1][S2]
- **Query fan-out:** the model issues several related sub-queries and assembles an answer from the results. A page
  can be cited for a sub-question it answers well. [S1]
- **The unit of value is shifting from the page to the supportable fact.** At least one engine grounds on "discrete,
  supportable facts with clear provenance". It checks fidelity, attribution and freshness, detects conflicts between
  sources, and may abstain when evidence is missing, stale or conflicting. [S2]
- **Answer engines without their own full index buy results from search providers** and pass rewritten queries to
  them. Then they fetch and cite pages. [S3][S4]
  - Consequence (inference): being well indexed and ranked in the major web search indexes is the base layer of AI
    visibility. No answer engine documents a separate "AI recipe" for content.
- **Citations are short.** At least one engine's citation object quotes at most 150 characters of the source. Another
  links whole sentences inline. [S4][S2] Write key facts as short, quotable, self-contained sentences.
- **One site rarely gets many slots:** at least one engine generally shows no more than two results from the same site
  in top results, and deduplicates near-identical results. [S1]
- **Money doesn't buy organic or AI placement.** Ads are separate and don't influence answers or rankings. [S1][S2][S3]
- **Timing:**
  - Changes show in "a few hours" to "several months"; judge after "a few weeks". [S1]
  - Recrawl takes days to weeks. [S1]
  - Robots.txt changes propagate within about 24 h. [S1][S3]

## 2. The rules that matter most

1. **Be reachable:** robots.txt allows the bots you want, **and** the CDN/WAF/bot protection doesn't block them (no
   403, 429, CAPTCHA or JS challenge for verified bot IPs). [S1][S2][S3][S4]
2. **Primary content, links and meta must be in the server-rendered HTML.** Some fetchers don't execute JavaScript
   at all. [S1][S2][S4]
3. **Non-commodity content:** first-hand, expert, specific, verifiable. Summaries of common knowledge lose.
   [S1][S2]
4. **Crawling ≠ indexing.** Use robots.txt to control crawling and `noindex` to control indexing. `noindex` is seen
   only on a crawlable page. [S1][S2][S3]
5. **One intent, one canonical URL.** Consolidate duplicates. [S1][S2]
6. **Clear structure:** title, H1 and description agree; descriptive headings; self-contained sections; key facts
   early and visible without interaction. [S1][S2]
7. **Structured data mirrors visible content.** It brings eligibility, never a guarantee, and no special markup
   exists for AI answers. [S1][S2]
8. **No manipulation:** no page farms per query variant, no engineered language to trigger citations, no prompt
   injection, no link schemes. [S1][S2]
9. **Every page you care about has a crawlable `<a href>` link and is in the sitemap** with an honest `lastmod`.
   [S1][S2]
10. **Measure in the engines' own consoles.** Third-party tools have no internal ranking or AI data. [S1][S2]
