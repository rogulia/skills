# Agent Skills

Skills for AI coding agents such as Claude Code, Codex and other tools that support the open
[Agent Skills](https://agentskills.io/specification) format. Each skill is a folder with a `SKILL.md` and reference files.

These are written **for agents, not for people**: compressed, rule-level, with sources, so an agent can apply them
directly instead of relying on its training memory.

## Skills

| Skill | What it does |
|---|---|
| [seo-geo-optimization](seo-geo-optimization/) | SEO and GEO (generative engine optimization) for search engines and AI answer engines. Built only from official vendor documentation, with an evidence-based audit procedure and a list of common SEO folklore that the docs don't support |

## Install only the skills you need

Every skill is independent. Install one, several or all.

### Claude Code: plugin marketplace

Each skill is also a plugin in this repository's marketplace. Add the marketplace once, then install skills by name:

```text
/plugin marketplace add rogulia/skills
/plugin install seo-geo-optimization@rogulia-skills
```

The same works from the shell with `claude plugin marketplace add rogulia/skills` and
`claude plugin install seo-geo-optimization@rogulia-skills`. Updates come through the marketplace.

### Codex

Ask the built-in installer to fetch the skill from this repository:

```text
$skill-installer install the seo-geo-optimization skill from https://github.com/rogulia/skills
```

### Any agent: copy one folder

Download only the folder you need, then copy it into your agent's skills directory:

```bash
git clone --depth 1 --filter=blob:none --sparse https://github.com/rogulia/skills.git
```

```bash
cd skills && git sparse-checkout set seo-geo-optimization
```

```bash
# Claude Code (all projects)
cp -r seo-geo-optimization ~/.claude/skills/
```

```bash
# Codex (all repositories)
cp -r seo-geo-optimization ~/.agents/skills/
```

For a single project, use `.claude/skills/` (Claude Code) or `.agents/skills/` (Codex) inside that repository. The
agent picks a skill up when a task matches its description; you can also call it by name.

## Author

Made by [Iurii Rogulia](https://iurii.rogulia.fi), Senior Full-Stack Software Engineer.

## License

[CC BY 4.0](LICENSE). Use, adapt and share, including commercially, with attribution.
