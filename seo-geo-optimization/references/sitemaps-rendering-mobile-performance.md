# Sitemaps, IndexNow, rendering, mobile parity, page experience

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 9. Sitemaps and change notification

### 9.1 Sitemaps [P][S1][S2]
- Formats: XML (also covers the image, video, news and hreflang extensions), RSS 2.0, Atom, plain text.
- Limits: **50,000 URLs or 50 MB uncompressed** per file; up to 50,000 sitemaps per index; gzip allowed; UTF-8;
  absolute, entity-escaped URLs.
- **List only canonical, indexable `200` URLs.** Remove deleted and redirected URLs promptly.
- **`priority` and `changefreq` are ignored.** `lastmod` is used only if accurate:
  - ISO 8601 with a time;
  - the time of the last significant change (main content, structured data, links), not the generation time;
  - a `lastmod` that always equals today gets ignored.
- Submit through robots.txt `Sitemap:` and the engines' consoles. One engine reads sitemaps about daily. Regenerate at
  least daily on changing sites. Don't resubmit unchanged sitemaps repeatedly.
- Small, well-linked sites (≈500 pages or fewer) may not need one. It's still cheap insurance. [S1]
- Cross-host sitemaps are allowed if each host is verified or referenced from its own robots.txt.
- Image sitemaps: up to 1,000 images per URL entry. `caption`/`title`/`license`/`geo_location` tags are **removed**
  from the protocol at one engine. [S1]

### 9.2 IndexNow (open push protocol) [P][S2]
- Notifies participating engines of added, updated or deleted URLs. A submission to one endpoint is shared with all
  participants. It isn't universal: some major engines don't participate (§A1).
- Mechanics:
  - `GET https://<endpoint>/indexnow?url=<url>&key=<key>`, or a POST with JSON `host`, `key`, optional `keyLocation`,
    and `urlList` (≤ 10,000 URLs).
  - Key: 8–128 characters `[a-zA-Z0-9-]`, hosted as `/{key}.txt`. One key per host.
- Responses:
  - `200` received (not indexed);
  - `202` key pending;
  - `400` bad format;
  - `403` invalid key;
  - `422` URL/key mismatch;
  - `429` "potential Spam".
- Rules:
  - submit only meaningful changes, **including deletions, redirects and 404/410**;
  - wait at least 5 min between submissions of the same URL;
  - a full-site push only after a migration;
  - submissions count toward the crawl quota;
  - indexing isn't guaranteed.
- Where IndexNow isn't supported: sitemaps with accurate `lastmod`, plus the console's URL-inspection "request
  indexing" for a few URLs.

## 10. Rendering and JavaScript

- Renderers queue pages after crawling (seconds or longer), then re-extract links from the rendered HTML. `200` pages
  are rendered; non-`200` pages may not be. Robots-blocked JS or pages are not rendered. [S1]
- **Some fetchers never render JS.** Unrenderable content "may not be indexed or selected for grounding". [S4][S2]
  → Server-side render or pre-render primary content, links, title, meta and canonical. [S1][S2][S4]
- Don't block the CSS, JS or images needed to render. [S1]
- SPA rules [S1]:
  - Use the History API, not `#/` routes.
  - For soft 404s, redirect to a URL that returns `404` or inject `noindex` on error views.
  - Exactly one canonical, the same in HTML and JS.
  - JSON-LD may be injected by JS; test it.
- Content only in the shadow DOM counts when it appears in the rendered HTML (use `<slot>`). CSS `content` text,
  canvas and plugin content aren't indexed. [S1]
- **Lazy loading:**
  - Load content when it enters the viewport (native `loading`, IntersectionObserver).
  - **Crawlers don't scroll, click, swipe or type.** Content that needs interaction isn't seen.
  - Don't lazy-load above-the-fold content. [S1]
- **Hidden-by-default content (tabs, accordions):** allowed and not treated as spam, but at least one engine's AI
  answers "may not render hidden content, so key details can be skipped". → Core facts visible by default; collapse
  only secondary detail. [S1][S2]
- Some fetchers send `Accept` headers that prefer Markdown. Serving Markdown via content negotiation is optional; no
  engine documents a ranking benefit from it. [S4]

## 11. Mobile parity [S1]

- At least one major engine indexes the **mobile** version only. Content missing on mobile isn't indexed.
- Responsive design is recommended. Dynamic serving must send `Vary: user-agent`.
- **Parity:**
  - the same primary content, headings, title, description, robots meta and structured data (mobile URLs inside
    mobile markup);
  - the same image URLs and alt text;
  - the same video markup;
  - no mobile-only `noindex`/`nofollow`;
  - no mobile resources blocked;
  - no interaction needed to load primary content.
- Separate mobile URLs:
  - the desktop page is canonical; the mobile page canonicalizes to it; the desktop page links
    `rel="alternate" media="only screen and (max-width: 640px)"`;
  - hreflang goes mobile↔mobile and desktop↔desktop;
  - error statuses match across versions;
  - every desktop page has a mobile equivalent;
  - the mobile host has enough capacity.

## 12. Page experience and performance

- Page speed and experience are ranking inputs, but **relevance beats experience**. Engines show the most relevant
  page "even if the page experience is sub-par". [S1][S2]
  - At least one engine lists its ranking parameters in order: relevance, quality/credibility, user engagement
    (clicks, dwell time, query reformulation), freshness, location/language, page load time. [S2]
- **Core Web Vitals "good" thresholds:** LCP within 2.5 s, INP < 200 ms, CLS < 0.1 (field data). They are used in
  ranking by at least one engine. Chasing a perfect score purely for SEO isn't worth it. [S1][P]
- **Lab ≠ field:** Lighthouse and similar lab runs diagnose problems. Only field data counts as CWV (§25.2).
- Other experience factors: HTTPS, mobile display, no excessive or intrusive ads, main content easy to identify.
  They don't directly raise rankings beyond CWV at one engine. [S1]
- **Interstitials:**
  - Full-page overlays and redirects to separate consent pages hurt understanding of the content.
  - Use small banners.
  - Legally required gates: overlay them on the content. Never redirect all URLs to one gate page; only that page
    would remain indexed.
  - Age gates: verify bots and serve them the content. [S1]
- **Error pages:**
  - A `404` page must return `404`. A page that returns `200` but says "not found", or is empty, is a soft 404 and gets
    excluded. [S1][S2]
  - Keep 404 pages static. [S2]
