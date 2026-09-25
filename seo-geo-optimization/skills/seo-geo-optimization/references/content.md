# Content that ranks and gets cited

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 14. Content that ranks and gets cited

### 14.1 Quality principles [S1][S2]
- **Non-commodity:**
  - First-hand experience, original research or data, an expert view, specific cases.
  - "7 tips"-style common knowledge loses. So does anything "easily … produced by a generative AI model".
  - The biggest long-term lever.
- **Trust, expertise, experience, authority** (trust matters most):
  - accurate bylines and author pages;
  - clear origination and ownership: first-hand information rather than summarized or republished content, clear publisher;
  - facts separated from opinion; satire labelled;
  - sources cited.
- **YMYL topics** (health, money, safety, society) get stricter weighting of these signals. [S1]
- **Satisfy the intent fully.** "Easy to understand without external context." [S2]
- **Search-engine-first red flags** (audit list) [S1]:
  - made for search visits;
  - scattered topics;
  - heavy automation;
  - mostly summarizing others;
  - chasing trends outside the audience;
  - the reader has to search again;
  - writing to a word count (no engine has a preferred length);
  - niche-hopping without expertise;
  - answering unanswerable questions;
  - **changing dates without substantial change**;
  - mass add/remove to look fresh.

### 14.2 Answer-ready page structure [S1][S2]
1. **One primary topic per URL.** Title, H1 and meta description say the same thing.
2. **Key information first**; no long preamble.
3. **Descriptive H2/H3s**, logical hierarchy, semantic HTML. Perfect HTML validity isn't required.
4. **Self-contained sections** understandable on their own; avoid long walls of text (they make content harder to
   split into usable pieces).
5. **Direct answers:**
   - 1–2 sentence answers under question-like headings.
   - Lists and tables for comparable facts.
   - Q&A pairs can be lifted "word for word" into answers.
   - Keep punctuation plain.
6. **Explicit, verifiable facts:** numbers, names, dates, units and sources on the page itself. Nothing "implied".
7. **Consistent entity names** for people, organizations, products and places, across the page and site.
8. **Media reinforce text:** descriptive filenames, alt text, captions and transcripts. Key info never lives only in
   images, video or PDFs.
- **Don't** split one topic into many thin pages, or write a page per phrasing or fan-out query (§15). No engine
  requires "chunking" into tiny pages. There is no ideal length. [S1]

### 14.3 Freshness [S1][S2]
- Update facts when they change and remove outdated ones. Then signal the change (sitemap `lastmod`, IndexNow).
- Show visible "Published" / "Updated" dates that match structured data (`datePublished`/`dateModified`, with the
  correct timezone). No future dates, and not the date of the described event.
- Fake freshness (bumping dates, churning content) doesn't help and is a red flag.
- At least one answer engine exposes each result's last-updated time to the model (`page_age`), so accurate dates
  matter for citation too. [S4]

### 14.4 AI-generated content [S1][S2]
- Allowed as a tool. Mass pages without added value = scaled content abuse. "Unreviewed auto-generated content at
  scale" is abuse.
- Auto-generated titles, descriptions, alt text and structured data must be accurate.
- Where readers would ask "How was this created?", disclose substantial AI or automation use.
- Product feeds: label AI-generated images (IPTC `DigitalSourceType` = `TrainedAlgorithmicMedia`) and AI-written
  titles and descriptions where the programme requires it. [S1]

### 14.5 Reviews and comparisons [S1]
- Show first-hand evidence: photos, measurements, test method.
- Cover:
  - differentiators;
  - alternatives and best-for use cases;
  - pros and cons from own testing;
  - changes from previous versions;
  - key decision factors.
- Justify "best" claims.
- Consider linking multiple sellers. Affiliate pages must add real value.
