# Hoplite Agent Skills

Public [Agent Skills](https://agentskills.io) for working with [Hoplite](https://hoplite.sh), the cloud coding-agent platform. Skills are plain `SKILL.md` folders that Claude Code, Codex, OpenCode and other agents load on demand.

## Install

Install one skill globally with the [`skills`](https://github.com/vercel-labs/skills) CLI:

```bash
npx skills add CarbonCopyInc/skills --skill handoff-to-hoplite --global
```

List everything available in this repository:

```bash
npx skills add CarbonCopyInc/skills --list
```

## Skills

| Skill | What it does |
| --- | --- |
| [`handoff-to-hoplite`](skills/handoff-to-hoplite/SKILL.md) | Continue a local Claude Code, Codex or OpenCode session in a live Hoplite cloud thread by running `hoplite handoff` safely. Requires the [Hoplite CLI](https://hoplite.sh/docs/cli). |

## Layout

Each skill lives in `skills/<name>/` with a `SKILL.md` (frontmatter `name` + `description`, then instructions) and optional agent-specific metadata under `agents/`.

## Contributing

Open a pull request. Keep skills self-contained, free of secrets and internal hostnames, and describe when the skill should be used in the `description` frontmatter so agents can pick it up unprompted.

## License

MIT. See [LICENSE](LICENSE).
