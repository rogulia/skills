# Bots, access, crawl limits and robots.txt

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 3. Bots and access

### 3.1 Bot classes — each is a separate decision

| Class | What it does | Effect of blocking |
|---|---|---|
| Search crawler | Builds the search index that classic results and AI answers draw on | Loss of search **and** AI-answer visibility |
| AI-search crawler (at engines that separate it) | Indexes for the answer engine's own search | Loss of visibility in that answer engine |
| Training crawler or control token | Collects content for model training | Training only; search is unaffected at engines that separate them [S1][S3][S4] |
| User-triggered fetcher | Fetches a page because a user asked | The engine can't read the page for users; robots.txt compliance varies (§24) |
| Ads / landing-page checker | Checks pages submitted as ads | Ads review only |
| Browser agent | Operates the site for a user (clicks, forms) | Agent tasks fail; the agent skips the site |

- At some engines the training opt-out is a **separate crawler**. At others it's a **control token** with no crawler
  of its own. At one engine there is **no separate token**: training is limited through meta directives (§5). [S1][S2][S3][S4]
- When both search and training crawls are allowed, an engine may reuse one crawl for both. [S3]
- Opt-outs of training apply to **future** crawls. Already-collected data isn't covered. [S4]

### 3.2 Reachability checklist
- **robots.txt:** the intended bots are allowed. A group for a specific bot **replaces** the generic `*` group rather
  than adding to it, so repeat the generic rules inside named groups. [S1][S2]
- **Some bots ignore `*` and obey only a group naming them explicitly** (e.g. ads checkers). [S1]
- **CDN/WAF:** allowlist by verified identity (published IP lists, reverse DNS, request signatures). Short-term IP
  observations from logs aren't reliable. [S2][S3]
- **Don't block bots by IP to opt out.** That also hides robots.txt from them, so the opt-out may not work. Use
  robots.txt. [S4]
- **Product images** referenced in feeds must be fetchable: a 403 on image URLs can block feed processing. [S3]
- **Bots don't solve CAPTCHAs** and don't create accounts. Content behind them doesn't exist for bots. [S4]

### 3.3 Verifying bot identity [S1][S2][S3][S4][P]
- User-agent strings are spoofable. Never trust the UA alone.
- Methods:
  - Reverse DNS on the IP → hostname in the engine's documented domain → forward DNS back to the same IP.
  - Match the IP against the engine's published IP-range JSON. Refresh it regularly (at least daily for one engine);
    don't hardcode ranges.
  - **Signed requests** (Web Bot Auth, RFC 9421 HTTP Message Signatures): verify the `Signature`,
    `Signature-Input` and `Signature-Agent` headers against the agent's public key directory. Proxies must preserve
    all three headers. This is how some browser agents identify themselves.
- Endpoints per engine: §A1.

### 3.4 Crawl limits (build for the strictest)
- **Only the first 2 MB of an HTML file** (uncompressed) is processed by at least one search crawler. Each CSS/JS
  resource has the same limit. PDFs have 64 MB. Keep content, meta tags, canonical, structured data and verification
  tags early in the HTML. [S1]
- Default for other crawlers of the same engine: the first 15 MB. [S1]
- Some fetchers accept **only text, HTML and PDF**. URLs longer than **250 characters** are rejected by at least one
  fetcher, and large pages are truncated. [S4]
- Rate: a well-behaved crawler should not hit a site "more than once every few seconds on average" (bursts possible). [S1]
- HTTP/2 is supported by some crawlers with "no ranking boost". Opt out with `421`. [S1]
- **Caching:**
  - Support `ETag`/`If-None-Match` and `Last-Modified`/`If-Modified-Since`, and return `304` when unchanged. [S1][S2]
  - Other cache directives are ignored by at least one crawler. [S1]
  - Fetcher caches may serve stale copies; one fetcher caches for 15 minutes. [S4]
  - Reference shared resources from one URL everywhere.
- **Fingerprint** static asset filenames (`main.2bb85551.js`): renderers may ignore cache headers and use stale JS/CSS. [S1]

### 3.5 Crawl rate control
- **`Crawl-delay` is supported by some engines and ignored by others.** [S1][S2][S4] Where it's supported, one engine
  treats the value as a 1–30 s window per page and gives it precedence over its console setting. [S2] Use it only if
  the owner accepts slower crawling on those engines. It isn't a universal throttle.
- **Emergency slowdown that works everywhere:** return `503` or `429` for hours up to **1–2 days**. The whole hostname
  slows down. Longer than ~2 days and URLs start dropping from the index. [S1]
- Never use `401`/`403` to limit crawl rate. [S1]
- Consoles may offer rate or schedule controls (§A1). Requesting a *higher* rate isn't possible at one engine. [S1][S2]

## 4. robots.txt (RFC 9309 + the strictest engine behaviour) [P][S1][S2][S4]

- One file per **protocol + host + port**, at the root. Subdomains need their own file. Files in subdirectories are
  ignored. No file on a host = no restrictions for that host.
- **UTF-8** plain text. Max **500 KiB** (content beyond it is ignored). Invalid lines are ignored.
- Portable fields: `user-agent`, `allow`, `disallow`, `sitemap`. `crawl-delay` is engine-dependent (§3.5). **No
  engine documents `noindex` in robots.txt**; one states it is unsupported.
- Field names are case-insensitive; **path values are case-sensitive** and start with `/`. A rule without a path is
  ignored.
- `sitemap:` takes an absolute URL, may be on another host, can appear any number of times, and isn't tied to a group.
- **Group selection:** the most specific matching user-agent group applies. Groups for the same UA merge. A specific
  group and `*` **don't combine**. Order in the file is irrelevant per the spec.
- **Matching:**
  - Prefix match; `*` = any sequence; `$` = end of URL; a trailing `*` is ignored.
  - `/fish` matches `/fish.html` and `/fish.php?id=1`, but not `/Fish.asp` or `/catfish`.
  - Non-ASCII paths are percent-encoded before matching.
- **Allow vs disallow:** the longest matching path wins; on a tie, the least restrictive (allow) wins.
  `allow: /$` + `disallow: /` → only the root is allowed.
- **Fetch outcomes:**
  - `2xx` → rules apply.
  - `3xx` → at least 5 hops followed.
  - `4xx` (except `429`) → **no restrictions**.
  - `5xx` or network error → crawling stops for 12 h; the last good copy is used for up to 30 days; then crawling
    resumes as if no file exists if the site is up, or stays stopped if the site is down.
- **Cache:** up to 24 h. Changes take hours to reach all engines.
- **A disallowed URL can still be indexed and shown** (URL + title from links, no description). robots.txt is not
  an indexing control. [S1][S2][S3]
- **Exception to know:** at least one answer engine lists disallowing its bots as a way to keep content out of its
  answers. Another engine's content-submission API indexes content that robots.txt disallows (it still honours
  `noindex`). Robots.txt isn't a guarantee in either direction. [S4][S2]
- Test with the console's robots.txt report or an open-source parser that matches the spec.
