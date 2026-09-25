# Agent Skills

Skills for AI coding agents such as Claude Code, Codex and other tools that support the open
[Agent Skills](https://agentskills.io/specification) format. Each skill is a folder with a `SKILL.md` and reference files.

These are written **for agents, not for people**: compressed, rule-level, with sources, so an agent can apply them
directly instead of relying on its training memory.

## Skills

| Skill | What it does |
|---|---|
| [seo-geo-optimization](seo-geo-optimization/) | SEO and GEO (generative engine optimization) for search engines and AI answer engines. Built only from official vendor documentation, with an evidence-based audit procedure and a list of common SEO folklore that the docs don't support |

## Install

Copy a skill folder into your agent's skills directory:

```bash
git clone https://github.com/rogulia/skills.git
```

```bash
# Claude Code: personal (all projects)
cp -r skills/seo-geo-optimization ~/.claude/skills/
```

```bash
# Codex: personal (all repositories)
cp -r skills/seo-geo-optimization ~/.agents/skills/
```

For a single project, use `.claude/skills/` (Claude Code) or `.agents/skills/` (Codex) inside the repository. The
agent picks the skill up when a task matches its description; you can also call it by name.

## Author

Made by [Iurii Rogulia](https://iurii.rogulia.fi), Senior Full-Stack Software Engineer.

## License

[CC BY 4.0](LICENSE). Use, adapt and share, including commercially, with attribution.
