# Spam and manipulation policies

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 15. Spam and manipulation — the union of all engines' policies [S1][S2]

Consequences range from demotion, loss of AI-answer or grounding eligibility, and ignored structured data up to
removal.

| Practice | Violation (discriminating detail) | Allowed |
|---|---|---|
| Cloaking | Different content to bots vs users, to manipulate | Paywalls if bots see what entitled users see, plus the paywall markup; A/B tests without bot-specific variants |
| Doorways | Near-identical pages per city or query variant funnelling to one destination | A real, browsable hierarchy |
| **Scaled content abuse** | Many pages made **for ranking, not users**, "no matter how it's created": AI mass pages, scraped or auto-transformed text, stitching, **pages per query variant or fan-out query** | Automation that adds real value |
| **Engineered language / AI manipulation** | Language written to "trigger citations or AI responses"; attempts "to manipulate generative AI responses" | — |
| **Prompt injection** | Content meant to manipulate or interfere with the engines' language models | — |
| Keyword stuffing | Keyword or number lists; blocks of city names; unnatural repetition | — |
| Hidden text/links | Text placed **solely** for bots, not easily visible | Tabs, accordions, tooltips, screen-reader-only text for accessibility |
| Link schemes | Links **primarily** to manipulate: paid, exchanged, automated, contract-required, optimized anchors in guest posts or press releases, widget/footer, PBNs, "artificial social promotion" | Paid links marked `sponsored`/`nofollow` |
| Scraping / thin affiliate | Republished or lightly rewritten content; merchant copy plus affiliate links without added value | Original reviews, testing, comparisons |
| Site reputation abuse | Third-party content hosted **mainly to exploit the host's signals** | Third-party content fully integrated under the host's editorial control |
| Hacked / injected content | Code, page or content injected through vulnerabilities; conditional redirects; hidden spam links | — (detect: §25.4) |
| Expired domain abuse | Repurposing an old domain's reputation for unrelated low-value content | — |
| Misleading functionality, malware, back-button hijacking, sneaky redirects | As named | Legitimate moves, post-login redirects |
| Misleading structured data | Markup not matching visible content; fake reviews | — |
| Policy circumvention | Moving violations to new subdomains or sites | — |
| Machine-generated queries | Automated scraping of result pages without permission | — |

- Duplicate content is **not** spam by itself (§6). [S1][S2]
- Some engines demote sites with many valid legal removals (copyright, defamation). [S1]
