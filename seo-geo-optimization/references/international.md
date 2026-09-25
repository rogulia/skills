# International and multilingual sites

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 13. International and multilingual [S1][S2]

- Language is detected **algorithmically from visible text**. Neither the `lang` attribute nor hreflang is used for
  that. Use one language per page.
- Use **separate URLs per language/locale**, not cookies or IP-based content. Crawlers mostly come from one country
  and send no `Accept-Language`.
- Never auto-redirect by guessed language. Link to the alternates and offer a selector on every page.
- **hreflang:**
  - Methods: HTML `<link>`, HTTP `Link` header (for PDFs), or sitemap `xhtml:link`. Pick one.
  - Each page lists itself and all alternates, with fully qualified URLs.
  - Links must be **reciprocal**, otherwise they're ignored. Partial sets are fine.
  - Codes: ISO 639-1, optionally with an ISO 3166-1 alpha-2 region (`en-GB`); scripts per ISO 15924 (`zh-Hant`).
  - A **region alone is invalid** (`be` = Belarusian). `es-419` isn't supported. `EU`, `UN` and `UK` are ignored.
  - `x-default` for selectors and redirecting home pages; a catch-all language page (`en`) when there are several locales.
  - Don't combine `hreflang` with `media` in one `<link>`. HTML links must sit in a valid `<head>`.
  - Same-language regional variants → canonical and hreflang together.
- **Geotargeting:**
  - Strong signal: ccTLD. Other signals: hreflang, local address, phone, currency, local links, a business profile.
  - Server IP is "not a definitive signal". Geo meta tags are ignored.
  - Some ccTLDs are treated as generic (e.g. `.io`, `.ai`, `.co`, `.me`, `.tv`, `.eu`).
- Locale-adaptive serving risks some locales never being crawled. If you gate by country, treat bots like users from
  their IP's country, and keep robots rules identical across locales.
