# Canonicalization, redirects, migrations, URLs and links

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 6. Canonicalization and duplicates [S1][S2]

- Duplicates are **not penalized by themselves**. They dilute signals, waste crawl, delay updates and lower confidence
  in choosing a URL to cite. Engines cluster near-duplicates and pick one representative. [S1][S2]
- **Signal strength:** redirect (strong) > `rel="canonical"` (strong) > sitemap inclusion (weak). Signals stack. All
  are hints. [S1]
- When content moves, **use redirects rather than canonicals**. Canonicals don't replace fixing the cause. [S2]
- `rel="canonical"`:
  - Only in `<head>`; an **absolute URL**; a self-canonical on the canonical page; one method per page (element or
    HTTP `Link` header, which is for non-HTML). [S1]
  - Ignored when it carries `hreflang`, `lang`, `media` or `type`. [S1]
- **Never:**
  - robots.txt, removal tools or `noindex` to steer canonicals;
  - a fragment URL as canonical;
  - conflicting canonicals across methods;
  - JS changing a canonical set in the HTML (JS may only repeat the same value). [S1]
- Link internally to canonical URLs. List only canonicals in sitemaps. [S1][S2]
- HTTPS is preferred as canonical, unless the certificate is invalid, dependencies are insecure, or HTTPS redirects or
  canonicalizes to HTTP. A bad certificate makes HTTP win "very strongly", and HSTS can't override it. [S1]
- **Syndication:** cross-domain canonical to the original, or publish excerpts. **Localization:** real translation plus
  hreflang. **Staging:** keep it out of crawl and index. [S2]
- Localized pages count as duplicates only if the **main content** stays untranslated. [S1]

## 7. Redirects and migrations

### 7.1 Redirect types [S1][S2]

| Type | Treatment |
|---|---|
| `301`, `308` (server-side) | Permanent; the target becomes canonical. Best option |
| Meta refresh 0 s / `Refresh: 0` | Permanent (weaker) |
| JS `location` | Permanent **only if rendered**; last resort; non-rendering fetchers never see it |
| `302`, `303`, `307` | Temporary; the source is kept; not a canonical signal. **Use only for very short changes (< 2 days)** |
| Delayed meta refresh | Temporary |

- Permanent redirects don't lose link value. Crawlers follow up to 10 hops; **keep chains ≤3**. Redirect straight to
  the final URL. [S1]
- Redirecting many URLs to an irrelevant page (e.g. the home page) is treated as a soft 404. [S1]
- Some fetchers don't follow cross-host redirects automatically, and inspection tools may not follow redirects at
  all. [S4][S1][S2]
- **A/B tests:** use `302` or JS redirects, never `301`. Canonicalize variants to the original. Crawlers carry no
  cookies. End tests promptly. [S1]

### 7.2 Site move procedure [S1][S2]
1. **Prepare:**
   - new site, TLS and the production robots.txt;
   - a plan for removing pre-launch `noindex`/blocks;
   - `404`/`410` for dropped content;
   - all host variants verified in **every** engine console;
   - crawl rate left to the engine;
   - for a bought domain, check existing manual actions and removals;
   - enough server capacity (crawling of the new site intensifies).
2. **Map:**
   - Collect old URLs from sitemaps, logs, analytics, console link reports and the CMS, **including images, JS and CSS**.
   - Build a 1:1 map.
   - On the new site: self-canonicals, updated hreflang, updated internal links.
3. **Redirect:**
   - Server-side `301`/`308`.
   - Small or medium sites move everything at once; large sites may move by section.
   - Change one thing at a time (domain, then CMS, then design).
4. **Launch:**
   - Test the redirects.
   - Use change-of-address tools where they exist (domain moves only).
   - Submit the new sitemaps; push the changed URLs via IndexNow where supported.
   - Update key external links, profiles and ad landing pages.
5. **Keep redirects for at least 1 year** (for users: indefinitely).
6. **Monitor** both sitemaps, index reports, logs and performance. Expect weeks of fluctuation.

## 8. URLs, links, site architecture

### 8.1 URLs [S1][S2]
- Follow STD 66; percent-encode reserved and non-ASCII characters.
- **No fragments (`#`) for content**; use the History API.
- Parameters: `key=value` joined by `&`; as few as possible; session IDs in cookies.
- Descriptive words in the audience's language; hyphens, not underscores.
- **URLs are case-sensitive**; normalize the case.
- **Stable URLs over time.** [S2]
- Keep URLs short: at least one fetcher rejects URLs longer than 250 characters. [S4]
- Crawl traps to block with robots.txt: additive facets, sort/referral parameters, infinite calendars, internal search
  results, and parent-relative link loops. [S1][S2]
- Group large sites (thousands of URLs) into topical directories. [S1]
- Every SPA view needs its own URL. [S1]

### 8.2 Links [S1][S2]
- Only `<a href>` with a real URL is reliably crawlable. Not crawlable: `onclick`-only elements, `href="javascript:…"`,
  `<span href>`, framework router attributes without `href`.
- Anchor text: descriptive and concise; an image's `alt` works as anchor text; no "click here", no stuffing.
- Every important page needs **at least one internal link**; engines infer importance from link depth and the number
  of internal links. URL structure is generally not used to infer hierarchy. [S1]
- Engines don't use on-site search boxes, so link everything you want indexed, or list it in sitemaps or feeds. [S1]
- Pagination: crawlable links between pages. Infinite scroll needs unique, persistent page URLs. [S1]
- Outbound qualifiers:
  - `rel="sponsored"` for paid links;
  - `rel="ugc"` for user content;
  - `rel="nofollow"` otherwise.
  - These are hints; the target can still be crawled via other paths. Citing sources helps trust. [S1]
- Link acquisition: a few links from trusted, relevant sites beat many random ones. A sudden spike (e.g. thousands in
  a day) is a red flag. [S2] Stricter rule applied: paid, exchanged, directory, widget and footer links meant to pass
  ranking are spam (§15). [S1]
