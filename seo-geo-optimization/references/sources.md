# Sources

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## Appendix A2 — Sources (fetched 2026-09-25)

- **[S1] Google Search Central** (≈86 pages).
  - Roots: `developers.google.com/search/docs/essentials` and `/fundamentals/ai-optimization-guide` (2026-07-10).
  - Everything they link, plus core technical docs:
    - robots.txt spec (2026-08-31), robots meta (2026-03-24), canonicalization (2026-08-20),
      consolidate-duplicate-urls (2026-07-10), redirects (2026-04-14), site moves (2026-08-20);
    - crawlers and fetchers (2026-06/09), HTTP status codes and DNS/network errors, crawl budget (2026-07-22);
    - sitemaps (2026-07-08), JavaScript SEO (2026-03-04), mobile-first, page experience (2026-09-22), CWV, localized
      versions (2026-09-21);
    - title links, snippets, site names, favicon (2026-08-28), images (2026-03-02), video, Discover;
    - structured data (intro, policies 2026-07-10, gallery 2026-06-15, organization/breadcrumb/paywall 2026-09-08);
    - spam policies (2026-08-28), helpful content, gen-AI content, ranking systems, traffic-drop debugging;
    - web.dev agent-UX (2026-04-01);
    - Search Console help 16908024 (Search generative AI control), 16984139 (Generative AI performance report),
      9008080 (verification).
- **[S2] Microsoft Bing** (≈54 pages).
  - Bing Webmaster help (live, undated): Webmaster Guidelines, AI Performance, crawlers, robots meta, robots.txt, crawl
    control, IndexNow, URL submission, sitemaps, removal, Block URLs, URL inspection, structured data, link building,
    404 pages, Bingbot verification.
  - blogs.bing.com:
    - AI Performance (2026-02-10), AI visibility insights (2026-06-16);
    - grounding (2026-02-12), index role (2026-05-06);
    - duplicate content (2025-12-19), data-nosnippet (2025-10-15);
    - sitemaps in AI search (2025-07-31), IndexNow (2025-05);
    - Bing Chat controls (2023-09), lastmod (2023-02), crawl-delay (2012-05).
  - about.ads.microsoft.com "Optimizing your content for inclusion in AI search answers" (2025-10-08).
  - support.microsoft.com "How Bing delivers search results" (2025-03).
  - indexnow.org documentation and FAQ.
- **[S3] OpenAI** (≈26 pages).
  - `developers.openai.com/api/docs/bots` and the bot IP JSON files.
  - help.openai.com:
    - publishers FAQ (≈2026-08-29), ChatGPT search, crawler allowlisting (≈2026-09-21);
    - agent allowlisting, Cloud browser, Atlas deprecation, training data;
    - shopping (≈2026-08-28), ads (2026-09-25).
  - `chatgpt.com/merchants`; `developers.openai.com/commerce/*` feed specs.
- **[S4] Anthropic** (≈19 pages).
  - support.claude.com: crawlers (2026-04-07), remove content (2026-09-03), web search (2026-09-22), Claude in Chrome.
  - `claude.com/crawling/bots.json`.
  - platform.claude.com web search, web fetch, server and browser tools; Claude Code tools reference.
  - Claude Opus 5.5 System Card §1.1.
- **[G] Public audit skills on GitHub** (process conventions only, no engine claims; surveyed 2026-09-25):
  - `joeseesun/qiaomu-seo`: evidence policy, coverage ledger, change stages, claim classes, AI-visibility ladder,
    content decision ledger, experiment design.
  - `zeebees/seo-geo-audit`: `PASS*`/`ABSENT` verdicts, `[proven]/[bet]/[contested]`, fetch-as-bot inference.
  - `aleksandr-alhoff/seo-landing`: `BLOCKER`, action boundary.
  - `AgriciDaniel/claude-seo`: deploy drift baseline (severities re-weighted here).
  - `JeffLi1993/seo-audit-skill`, `onvoyage-ai/gtm-engineer-skills`: script + LLM split; no fabricated scores.
  - `Suganthan-Mohanadasan/tech-seo-audit-skill`: impact/effort prioritization.
  - `coreyhaines31/marketingskills`: AI-mention framing. Several of its engine claims contradict first-party docs
    and were rejected (§23).
  - Numeric thresholds and composite scores from these repos were **not** adopted.
- **[P] Standards and protocols:** RFC 9309 (robots.txt), RFC 9421 (HTTP Message Signatures / Web Bot Auth), STD 66
  (URIs), sitemaps.org protocol, schema.org, ISO 639-1 / 3166-1 / 15924, IndexNow, Core Web Vitals (web.dev),
  Chrome Lighthouse llms.txt audit (developer.chrome.com, 2026-05-05).
- **Staleness to watch:**
  - [S3]: the Atlas browser is deprecated, but some publisher-FAQ wording still refers to it. Instant Checkout is
    de-emphasized.
  - [S2]: crawl-delay semantics come from 2012. One guideline mentions an undocumented `data-snippet` attribute.
  - [S4]: which product sends which UA is mostly undocumented.
