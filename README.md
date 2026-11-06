# .github

Default community health files for my repositories. GitHub uses `pull_request_template.md`
for any repo of mine that doesn't have its own template.

## Skills

Claude Code skills I use across projects. Install one by copying its folder into
`~/.claude/skills/` (global) or `<repo>/.claude/skills/` (one project).

| Skill | What it does |
|---|---|
| [`contractor-workflow`](skills/contractor-workflow/SKILL.md) | Before coding: investigate, size by risk, write a brief. After: a PR with a risk map (🔴/🟡/🟢), decisions, evidence and how to check it. Pairs with `pull_request_template.md`. |
| [`repo-inspect`](skills/repo-inspect/SKILL.md) | Security-inspects a GitHub repo through the API, without cloning or running it: auto-execution traps, malware patterns, supply chain, prompt injection aimed at AI agents. Ends with a 0–100 trust score and the exact lines. Usage: `/repo-inspect owner/repo`. |

```sh
cp -R skills/repo-inspect ~/.claude/skills/
```
