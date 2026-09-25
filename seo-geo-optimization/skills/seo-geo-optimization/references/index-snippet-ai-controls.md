# Index, snippet, AI-answer and training controls

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 5. Index, snippet and AI-use controls

### 5.1 Decision table

| Goal | Use | Never |
|---|---|---|
| Stop crawling (load, infinite spaces) | robots.txt `Disallow` | — |
| Keep a page out of results and AI answers | `noindex` (meta or `X-Robots-Tag`) on a **crawlable** page [S1][S2][S3] | robots.txt disallow (the `noindex` is never seen) |
| Keep from everyone | Authentication | — |
| Permanent removal | Delete (`404`/`410`), authentication, or `noindex` | robots.txt |
| Urgent temporary hide | Console removal tools (≈90 days to ~6 months, depending on engine; cover every URL variant) [S1][S2] | Using removal tools for canonicalization |
| Media out of results | Disallow the file in robots.txt **or** `X-Robots-Tag: noindex` on the file (not both) [S1][S4] | — |
| Stay in search, limit AI-answer use | Engine-specific (§5.3) | Assuming one engine's control covers the others |
| Opt out of model training | Engine-specific training token or directive (§3.1, §A1) | Blocking search crawlers for this (it kills search visibility) |

### 5.2 Robots meta / `X-Robots-Tag` rules

| Rule | Effect | Support |
|---|---|---|
| `noindex` | Not in results. At some engines also excluded from AI answers, grounding APIs and training | Universal [S1][S2] |
| `nofollow` | Don't follow links on the page | Universal |
| `none` | `noindex, nofollow` | [S1] |
| `nosnippet` | No text snippet or video preview. At least one engine also bars the page from use as direct AI-answer input; another says it "may limit" citation quality | [S1][S2] |
| `max-snippet:[n]` | `0` = nosnippet, `-1` = no limit; also limits AI-answer input at one engine | [S1][S2] |
| `max-image-preview:none\|standard\|large` | Image preview size; `large` is needed for large-image feeds | [S1][S2] |
| `max-video-preview:[s]` | `0` = static image only; `-1` = no limit | [S1][S2] |
| `noarchive` | At one engine: excluded from AI answers and grounding, and not used for training. Others: no longer used | [S2] vs [S1] |
| `nocache` | At one engine: AI answers may show only URL, title and snippet. Others: not used | [S2] vs [S1] |
| `notranslate`, `noimageindex`, `indexifembedded` (with `noindex` only), `unavailable_after: [date]` | As named. After `unavailable_after`, crawling of the URL drops sharply | [S1] |

- **Precedence:**
  - Conflicting rules → the **more restrictive** wins.
  - Rules for several crawlers add up.
  - `noarchive` + `nocache` together → treated as `nocache` at the engine that uses them. [S1][S2]
- Robots meta is honoured even in `<body>` by at least one engine; put it in `<head>`. `name="robots"` = all bots;
  a crawler-specific name targets one engine. Names and values are case-insensitive. [S1][S2]
- **Non-HTML files** (PDF, images, video) use the `X-Robots-Tag` header. Use server rules for site-wide or regex
  control. [S1][S2]
- **`data-nosnippet`** on `span`, `div` or `section` excludes that text from snippets and from AI answers; the page is
  still indexed and ranked. [S1][S2]
  - It is boolean (any value = on). An unclosed tag swallows the rest of the page.
  - Set it at element insertion; don't toggle it via JS.
- Snippet rules don't restrict structured-data use except `description` fields. [S1]
- `noindex` placed in the initial HTML may stop rendering entirely, so **removing** it with JS may never be seen.
  **Adding** it with JS works. [S1]

### 5.3 AI-answer and training controls — the three independent owner decisions
1. **Search visibility.** Controlled by search-crawler access and `noindex`.
2. **Use in AI answers while staying in search.** Available mechanisms differ per engine:
   - a console switch that excludes the site from AI features (Google Search Console → Settings → Search
     generative AI, since 2026-08-31; takes effect in days; not a ranking signal elsewhere) [S1];
   - `noarchive`/`nocache` [S2];
   - snippet limits and `data-nosnippet` [S1][S2];
   - at answer engines whose search is the AI surface, there's no separate switch: blocking their search crawler is
     the only lever [S3][S4].
3. **Model training.** A training crawler, training token or meta directive, per engine (§A1).
   - At least one engine has **no training-only opt-out that keeps full AI-answer use**. [S2]
   - Opt-outs are forward-only. [S4]

Agents report the current state and its trade-offs. The owner decides.

### 5.4 Meta tags: used vs ignored
- **Used:** `title`, `description`, robots directives, charset (UTF-8), `viewport` (signals mobile-friendliness),
  verification tags (case-sensitive values), `rating` = adult. [S1][S2]
- **Ignored:**
  - `meta keywords` ("dead in terms of SEO value"; "not used") [S1][S2];
  - the HTML `lang` attribute for language detection;
  - `rel=next/prev`;
  - geo meta tags [S1].
- `<head>` must be valid HTML, with all parent tags closed. Avoid JS-injected meta tags; if unavoidable, test them. [S1]
