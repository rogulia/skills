# Engine reference: crawler tokens, verification, consoles

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## Appendix A1 — Engine reference (names needed for configuration)

| Engine | Search / index crawler | AI-search crawler | Training control | User-triggered | Agents | Verification | Console |
|---|---|---|---|---|---|---|---|
| Google | `Googlebot` (+`Googlebot-Image`, `-Video`, `-News`, `Storebot-Google`) | — (AI features use the search index) | `Google-Extended` (control token, no own UA; Gemini training and grounding; no effect on Search) | e.g. `FeedFetcher-Google`, `Google-Read-Aloud` (generally ignore robots.txt) | `Google-Agent` (testing Web Bot Auth: `https://agent.bot.goog`) | reverse DNS `googlebot.com` / `google.com` / `googleusercontent.com`; IP JSON lists | Google Search Console (Generative AI performance report; Settings → Search generative AI) |
| Bing / Microsoft Copilot | `bingbot` (desktop + mobile); `BingPreview`, `MicrosoftPreview`; ads `AdIdxBot` | — (Copilot and grounding APIs use the Bing index) | No token; `noindex` / `noarchive` / `nocache` | — | — | reverse DNS `search.msn.com`; `https://www.bing.com/toolbox/bingbot.json` | Bing Webmaster Tools (AI Performance; IndexNow; Block URLs 90 days; Crawl Control) |
| OpenAI / ChatGPT | — (uses third-party search providers too) | `OAI-SearchBot` (blocked → not in ChatGPT search answers; link may remain) | `GPTBot` (training only; independent of search) | `ChatGPT-User` (robots.txt "may not apply") | Cloud browser: Web Bot Auth, `Signature-Agent: "https://chatgpt.com"`; keys at `https://chatgpt.com/.well-known/http-message-signatures-directory` | `https://openai.com/searchbot.json`, `gptbot.json`, `chatgpt-user.json`, `adsbot.json` | None; referral tag `utm_source=chatgpt.com`. Ads bot: `OAI-AdsBot` |
| Anthropic / Claude | — (search via partners; images via Bing) | `Claude-SearchBot` | `ClaudeBot` (future materials only) | `Claude-User` (robots-controllable; API web fetch enforces robots.txt, no JS) | Claude in Chrome runs in the user's browser (no own UA) | `https://claude.com/crawling/bots.json` (IPv4) | None; takedown via `webresultsoptout@anthropic.com` |

- IndexNow participants listed by indexnow.org: Bing, Yandex, Naver, Seznam.cz, Yep, Amazon. Google is not on that list.
- `crawl-delay`: honoured by Bing and Anthropic; not supported by Google; not stated by OpenAI.
- `noarchive` / `nocache`: active at Bing (AI/Copilot and training); "no longer used" at Google.
