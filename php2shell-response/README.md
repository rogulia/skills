# PHP RCE response skill for AI agents (CVE-2026-109618, CVE-2026-109619)

An [Agent Skill](https://agentskills.io/specification) that makes your AI agent (Claude Code, Codex and others)
triage the two critical PHP remote code execution flaws patched on 2026-10-21 ("PHP2Shell"): find every PHP runtime
that needs the fix, judge how exposed it is, plan the update and verify it.

**What it does**
- **Inventory.** Finds PHP on hosts, in container images, in CI and in deploy configs, and separates the version the
  CLI reports from the version the web server actually runs.
- **Exposure.** Checks the SAPI, the settings the advisories name, and the frontend configuration that can feed
  PHP settings per request.
- **Plan.** An update route per platform, plus the interim mitigations from the PHP advisory, labelled as partial.
- **Verify.** Checks the running process, not the package list; says what a clean log does and does not prove.

**Basis.** The two PHP security advisories (GHSA-w8hw-w9fx-j79w, GHSA-6wmf-4rv5-p3rp) and the PHP release
advisory of 2026-10-21. Facts the advisories do not state are marked as unverified.

**Read-only by default.** The skill diagnoses and recommends. It does not change configuration, restart services or
run exploit code.

**Contents:**
- `skills/php2shell-response/SKILL.md`: the procedure and the rules.
- `skills/php2shell-response/references/`: advisory facts, diagnostic commands, remediation routes and sources.
- `plugin.json` makes the folder a portable [Agent Plugin](https://agent-plugins.org).

**Install:** see the [repository README](../README.md#install-only-the-skills-you-need) for Claude Code, Codex,
Cursor or a plain folder copy.

**Maintained by** [Iurii Rogulia](https://iurii.rogulia.fi).

License: [CC BY 4.0](../LICENSE).
