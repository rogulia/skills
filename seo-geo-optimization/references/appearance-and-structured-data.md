# Result appearance and structured data

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 16. How results and citations look

### 16.1 Titles [S1][S2]
- A unique, descriptive `<title>` on every page. **There is no length limit**; titles are truncated to the device
  width. Missing, duplicate or too-short titles and descriptions hurt indexing reliability and AI-grounding
  eligibility. [S2]
- Engines rewrite titles that are:
  - half-empty;
  - obsolete (wrong year);
  - inaccurate;
  - boilerplate across different pages;
  - ambiguous (several equal headings);
  - in a different language or script from the content;
  - repeating the site name.
  Sources for rewrites include the H1, headings, `og:title`, anchor text and inbound link text. [S1]
- Brand concisely with a `-`, `:` or `|` delimiter. No stuffing.

### 16.2 Snippets and descriptions [S1][S2]
- Snippets come **mainly from page content**, per query. The meta description is used when it describes the page
  better.
- Meta description: **no length limit**; unique per page; can be structured (author, date, price). Programmatic
  generation is fine if the result is readable and varied. Keyword lists get ignored.
- Deep links to page sections need that content visible on load, no forced scroll on load, and hash fragments left
  intact. [S1]
- **Answer boxes / featured snippets** can't be requested. [S1]
  - Opt-out options:
    - `nosnippet` (guaranteed; removes all snippets);
    - `data-nosnippet` (specific text);
    - lowering `max-snippet` *may* suppress only the answer box (not guaranteed).
  - If both `nosnippet` and `data-nosnippet` are set, `nosnippet` wins.
- **Sitelinks** are automated. There's no owner control except removing the page or `noindex`. Influence them through
  clear titles and headings, logical internal links and concise anchors. [S1]
- Related searches and "people also ask" blocks aren't controllable. [S1]

### 16.3 Site identity [S1]
- **Site name:**
  - `WebSite` structured data (`name`, `url`, `alternateName`) on the **home page** of each domain or subdomain
    (not subdirectories).
  - Consistent with `og:site_name`, `<title>` and headings.
  - The same markup on every duplicate home page.
  - Fallbacks: add `alternateName`, including the lowercase domain.
- **Favicon:**
  - `<link rel="icon">` on the home page; one per hostname; square; **≥ 8×8 px, recommended > 48×48 px**; a stable URL
    fetchable by image crawlers.
  - Formats BMP, GIF, ICO, PNG, JPEG, PPM, TIFF.
- **Logo** (Organization markup): **≥ 112×112 px**, crawlable, looks right on white.

### 16.4 Feeds and large-image surfaces [S1]
- Large previews need `max-image-preview:large` and a representative image: **≥ 1200 px wide, > 300,000 px total,
  16:9**, via `og:image` or schema `image`. Not a logo, not text-heavy.
- Clickbait and sensationalism are filtered out. Traffic from feeds is volatile and supplemental.

### 16.5 Images [S1][S2]
- Use `<img src>` (`<picture>` with an `<img>` fallback; `srcset` plus `src`). **CSS background images aren't indexed.**
- Formats: BMP, GIF, JPEG, PNG, WebP, SVG, AVIF. One URL per image across pages.
- **Alt text is the most important image signal**, and it doubles as anchor text for image links. Describe the image
  in context, without stuffing. Filenames are "very light clues".
- Place images near relevant text. Captions matter.
- Preferred preview image: `primaryImageOfPage`, the main entity's `image`, or `og:image`.
- Label adult content for safe-search filters.

### 16.6 Video [S1][S2]
- Video features need a **dedicated watch page** whose main purpose is one video, embedded via
  `<video>`/`<iframe>`/`<embed>`/`<object>`. Nothing may require interaction to load it.
- The page must be indexed and perform well before its video is considered.
- `VideoObject` markup: `embedUrl` (player) and `contentUrl` (file); or a video sitemap.
- Thumbnail: required; at a stable URL; the same across metadata; **≥ 60×30 px**; ≥ 80 % of pixels with alpha > 250.
- Let crawlers fetch the video file (previews, key moments); expiring CDN URLs block indexing.
- Key moments via `Clip` or `SeekToAction` markup; opt out with `nosnippet`.
- Captions and transcripts make video content usable as text. [S2]

## 17. Structured data (schema.org) [S1][S2][P]

- **Formats:** JSON-LD (recommended, easiest to maintain), Microdata or RDFa. Engines don't prefer one over another.
  JSON-LD may be injected by JS; test the result.
- **Must:**
  - mark up only **visible** content, on the page it describes;
  - be current, original and representative of the main content;
  - use the most specific type;
  - include all required properties (fewer accurate properties beat many sloppy ones);
  - crawlable image URLs;
  - the same markup on all duplicates;
  - mark up **all** visible items of a kind;
  - include the page's main-focus type;
  - link separate items with `@id`.
- **Never:**
  - fake reviews, or ratings not from real users;
  - impersonation;
  - irrelevant types;
  - blocking marked-up pages from bots.
- **Effects:**
  - Eligibility for rich results only; never guaranteed; not in every market.
  - Invalid or misleading markup is ignored or triggers a manual action (lost rich results).
  - For AI answers, structured data "may support clearer grounding but does not guarantee visibility". There is no
    special AI schema.
- **Validation:**
  - Validator for syntax, then deploy a few pages and inspect them in the console. Monitor enhancement reports.
  - Rising invalid items = broken template. Falling valid items = markup removed.
  - Quality problems aren't caught by validators.
- **High-value types:**
  - `Organization` (home or About page: `name`/`alternateName` matching the site name, `url`, `logo`, `address`,
    `telephone`, `contactPoint`, `sameAs`, legal identifiers such as `vatID`, which is "an important trust signal");
  - `WebSite` (site name);
  - `BreadcrumbList` (≥ 2 items, a user path, not the URL);
  - `Article`/`BlogPosting` (dates);
  - `Product`, including offers;
  - `LocalBusiness`;
  - `VideoObject`;
  - paywall markup (`isAccessibleForFree: false` plus `hasPart.cssSelector` using a class selector) to separate
    paywalls from cloaking.
- The set of rich-result types an engine supports changes. Check the engine's current gallery before promising one.
  FAQ and HowTo are not in one major engine's current list. [S1]
