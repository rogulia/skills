# Commerce, local, browser agents, llms.txt

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 18. Commerce and local [S1][S2][S3]

- **Three product-data channels, used together:**
  1. `Product`/`Offer` markup on crawlable product pages;
  2. merchant feeds submitted to each engine's merchant programme;
  3. push notifications of changes. At least one engine says IndexNow plus Product markup can replace a separate feed.
- **Feed essentials** (common required fields):
  - a stable item ID;
  - title and description in plain text;
  - canonical product URL;
  - brand; seller name;
  - image URL (fetchable; a 403 blocks processing);
  - availability from a fixed vocabulary;
  - price with currency.
- **Feed discipline:**
  - One row per variant, with a group ID and an ID that never contains the price.
  - A valid GTIN (with check digit) where it exists.
  - No placeholder values (`null`, `n/a`). Empty is not zero.
  - Missing shipping = unknown, not free.
  - Tracking parameters stay consistent across snapshots.
- **Removal lag:** a product dropped from a feed may stay visible for up to 14 days. Use the explicit
  "not eligible" flag for immediate removal. Dates in feeds don't schedule changes at some engines. [S3]
- **How answer engines rank merchants for the same product:** "availability, price, quality, and whether they are
  the maker or primary seller". [S3]
- Programmes differ in availability: some are invite-only or country-limited. Check before promising.
- **Local:**
  - Claimed and complete business profiles on each engine's local platform feed AI answers and maps.
  - Keep name, address, phone and hours identical everywhere.
  - Some answer engines use third-party reservation and review platforms for local data.
- Never invent prices, availability or product claims. The project's product data is the only source.

## 19. Browser agents [S1][S3][S4][P]

- Agents read the page through **screenshots**, the **DOM** (containment implies ownership) and the **accessibility
  tree**.
- Make the site operable:
  - semantic `<button>` and `<a>`; `role` and `tabindex` where semantics are impossible;
  - `<label for>` on every input;
  - ARIA roles, labels and states on interactive widgets;
  - `cursor: pointer` on actionable elements;
  - actionable elements visible and "larger than 8 square pixels";
  - no transparent overlays over controls;
  - key actions in stable positions.
- **Identification:** agents increasingly sign requests (Web Bot Auth, RFC 9421). CDN presets exist for common agents.
  Decide per project whether agent traffic is wanted. Blocked agents skip the site ("either skip those sources or
  rely on other sites").
- Agents don't bypass CAPTCHAs or create accounts. They may ask the user to sign in.
- Emerging protocols: WebMCP (declared agent tools, proposed standard, experimental); agentic-commerce protocols with
  discovery via `/.well-known/…` and signed or tokenized payments. Treat them as optional until the owner opts in.
- Fetch parameters in URLs may be retained by the site when an agent fetches it, so never put secrets in URLs you
  hand to agents. [S4]

## 20. llms.txt and other "AI files"

- **No engine documents llms.txt, AI text files, Markdown copies or special markup as a ranking, indexing or citation
  input.** One engine states it ignores them and that they "neither harm nor help". [S1]
- Some vendors publish llms.txt for their **own** documentation. That isn't a claim that they read other sites'
  files. [S3][S4]
- A browser vendor's lab audit (Lighthouse, "Agentic browsing") checks `/llms.txt`:
  - a server error is flagged;
  - a 404 is "Not Applicable", "as providing the file is optional at the moment".
  - It's a lab agent-UX check, not a search or citation input. [P]
- Rule: optional, zero-risk, not a visibility lever. Never sell it to a client as one. If it exists, it must return
  `200` (never `5xx`) and stay accurate.
