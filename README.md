# Agent Skills

Skills for AI agents: Claude Code, Codex, Cursor and any tool that supports the open
[Agent Skills](https://agentskills.io/specification) format. Each skill is packaged as a portable
[Agent Plugin](https://agent-plugins.org), so it installs through a plugin marketplace or as a plain skill folder.

These are written **for agents, not for people**: compressed, rule-level and sourced, so an agent can apply them
directly instead of relying on its training memory.

## Skills

| Skill | What it does |
|---|---|
| [seo-geo-optimization](seo-geo-optimization/) | SEO and GEO (generative engine optimization) for search engines and AI answer engines. Built only from official vendor documentation, with an evidence-based audit procedure and a list of common SEO folklore that the docs don't support |
| [php2shell-response](php2shell-response/) | Triage and remediation for the PHP2Shell remote code execution flaws CVE-2026-109618 and CVE-2026-109619 (patched 2026-10-21): find affected PHP runtimes, assess exposure, plan the update, verify the fix. Built from the two PHP security advisories |

## Install only the skills you need

Every skill is independent. Install one, several or all. Replace `seo-geo-optimization` with the skill you want.

### Claude Code

```text
/plugin marketplace add rogulia/skills
/plugin install seo-geo-optimization@rogulia-skills
```

From the shell: `claude plugin marketplace add rogulia/skills`, then
`claude plugin install seo-geo-optimization@rogulia-skills`.

### Codex / ChatGPT

```bash
codex plugin marketplace add rogulia/skills
```

Then install the plugin from the **rogulia-skills** marketplace in the plugin browser (`/plugins` in Codex CLI, or the
Plugins Directory in the ChatGPT desktop app).

### Cursor

Open **Customize** → **From GitHub Repository** → `rogulia/skills`, then install the plugin you need.

### Any other agent: copy the skill folder

```bash
git clone --depth 1 --filter=blob:none --sparse https://github.com/rogulia/skills.git
```

```bash
cd skills && git sparse-checkout set seo-geo-optimization
```

```bash
cp -r seo-geo-optimization/skills/seo-geo-optimization ~/.agents/skills/
```

`~/.agents/skills/` is read by Codex and Cursor. Claude Code uses `~/.claude/skills/`. For a single project, use
`.agents/skills/` or `.claude/skills/` inside that repository.

## Repository layout

```text
.claude-plugin/marketplace.json    catalog for Claude Code (Codex also reads it)
.cursor-plugin/marketplace.json    catalog for Cursor
.agents/plugins/marketplace.json   catalog for Codex / ChatGPT
<plugin>/plugin.json               Agent Plugins 1.0.0 manifest
<plugin>/skills/<skill>/SKILL.md   Agent Skills format, plus references/
```

## Author

Made by [Iurii Rogulia](https://iurii.rogulia.fi), Senior Full-Stack Software Engineer.

## License

[CC BY 4.0](LICENSE). Use, adapt and share, including commercially, with attribution.
