# Audit and change procedure

Part of the `seo-geo-optimization` skill. Section numbers (§) are shared across all files; see `SKILL.md` for the index.

## 25. Audit and change procedure

*(Process conventions borrowed from public audit skills [G]; they carry no engine claims.)*

### 25.1 Work modes and the action boundary
- Modes:
  - advisory (question, no fetch);
  - single page;
  - template sample;
  - site inventory;
  - incident (drop or de-indexing);
  - migration;
  - experiment.
- Name the mode before starting.
- **Audit modes are read-only.** Changing index controls, bot rules, AI/training opt-outs, submitting or removing URLs,
  disavowing, and mass content changes need explicit owner authorization (§0).

### 25.2 Evidence rules
- **Audit from raw bytes.** Use raw HTTP (`curl -sI` / `curl -sL`) and the rendered DOM (a headless browser or the
  console's live inspection).
  - Never judge markup, JSON-LD, headers or status codes from a summarizing fetch tool. Such tools return a model's
    answer, truncate pages, cache them and may not follow redirects. [S4]
- **Quote evidence verbatim:** the header line, the tag, the log line, the command output. Paraphrase is not evidence.
- **Fetch-as-bot with a spoofed UA is only indicative.** A 403 to a spoofed bot UA is a WARN: engines verify by IP, so
  the real bot may pass. Confirm through server logs (verified IPs, §3.3) or the engine's own inspection tool.
  - Compare bot vs human responses by **content**, not bytes (nonces and timestamps are noise).
- **When a check can't run** (blocked, login, tool failure), report `BLOCKER: <reason>`. Never estimate a result.
- **Lab ≠ field.** Lighthouse or other lab runs are diagnostics. Only field data (the console's CWV report or real-user
  data) may be reported as Core Web Vitals (§12).
- **Coverage ledger** before any breadth claim: URLs discovered / selected / fetched / rendered / data-backed / failed /
  not checked.
  - Sample by template and risk. A sample is reported as a "representative template sample", never as a full-site audit.

### 25.3 Finding format

| Field | Values |
|---|---|
| Verdict | `PASS` · `PASS*` (passes, but something adjacent cancels the benefit, e.g. no cloaking yet both bot and user get an empty JS shell) · `WARN` · `FAIL` · `ABSENT` (an optional, unproven practice is missing; never a FAIL) · `NOT_CHECKED` · `BLOCKER` |
| Evidence level | observed · inferred · missing (missing evidence can't produce PASS or FAIL) |
| Basis | the §-rule and its source tag. `[proven]` = engine-stated or plainly breaks something; `[bet]` = cheap, unproven (e.g. llms.txt); `[contested]` = engines disagree (§24) |
| Severity | Critical = blocks crawling or indexing of important pages, or is a spam or security risk · High = loses rankings or citations on key pages · Medium = efficiency or clarity · Low = polish |
| Impact / confidence | stated separately; high impact with low confidence is a hypothesis, not a finding |
| Fix | the change, its effort, dependencies, reversibility, owner, and **how to verify it** |

- Order: Critical first, then impact ÷ effort. Keep **safe fixes** separate from **owner decisions**.
- Every row is filled. A PASS states what must keep being true.
- No composite 0–100 health scores or A–F grades; their weights are arbitrary. Unknown is excluded, never scored as zero.
- Don't recommend something solely because a competitor has it.

### 25.4 Audit layers (in order)
1. **Scope:** hosts, locales, templates, key page types, the owner's AI/training decisions, the mode.
2. **Access:** robots.txt, WAF/CDN, bot verification, status codes, redirects, sitemaps, raw vs rendered HTML.
3. **Index:** console indexing reports, canonicals, duplicates, mobile parity, hreflang.
4. **Integrity:** hacked or injected content and prompt injection:
   - hidden text or links;
   - invisible Unicode;
   - instructions to LLMs in HTML comments, `alt`, `aria-*` or off-screen elements;
   - unexpected outbound links;
   - spammy `site:` query results;
   - the console's security and manual-action reports. [S1][S2]
5. **Content:** intent coverage, answer-readiness (§14.2), entity consistency, freshness, structured data vs visible content.
6. **Appearance and measurement:** titles, snippets, AI-answer reports, referral segments.

### 25.5 Change lifecycle — claims per stage
1. **Implemented** (in code/CMS) → 2. **Deployed and observable** (live bytes confirm it) → 3. **Processed** (engine
   recrawled; inspection shows it) → 4. **Outcome observed** (metrics over the stated lag, weeks).
- Only stage 4 supports a claim that the change "worked". Report the stage reached.

### 25.6 Regression guard after deploys
- Keep a per-URL baseline for key templates: status, title, meta description, canonical, robots meta / `X-Robots-Tag`,
  hreflang, H1–H3, JSON-LD hash, Open Graph image. Diff after every deploy.
- **Critical:** `noindex` added; canonical changed; status became 4xx/5xx or a redirect; robots.txt newly disallows key
  paths; structured data removed. Heading wording changes are **not** critical (§23).

### 25.7 Content decisions and scaled pages
- Per page or cluster: **keep / improve / consolidate (301) / reposition / remove**. Each needs its own evidence.
  Never mass-delete pages just for having zero clicks.
- **Gate before scaling templated or programmatic pages:**
  - real user demand for each variant;
  - page-specific value beyond swapped variables;
  - the provenance of the data;
  - a staged launch with a hold-out group;
  - a rollback plan.
  Failing the gate = scaled content abuse risk (§15).

### 25.8 Experiments
- Record:
  - the hypothesis;
  - the treatment unit (a template group, not single pages);
  - the baseline window;
  - guardrail metrics;
  - the stopping rule and measurement lag.
- Report null results. Use stable, non-seasonal pages.

### 25.9 Source discipline
- Classify every claim:
  - stable principle;
  - current engine rule (source and date);
  - observed market state;
  - estimate;
  - hypothesis.
- Engine rules older than their doc's "last updated" date get re-read before use. Conflicts are recorded, not
  silently resolved.
- Label features experimental / limited / supported / deprecated / removed.
