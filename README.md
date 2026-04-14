# nudge

Your AI product advisor for GitHub projects.

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that analyzes your GitHub repos and generates concrete, actionable suggestions — posted as GitHub issues with agent-ready prompts.

## What it does

1. **Reads** your repo deeply — README, commits, issues, file tree, manifest, and local clone if available
2. **Diagnoses** what stage your project is in and what matters most right now
3. **Suggests** one high-ROI next step with specific evidence from your project
4. **Posts** it as a GitHub issue with a prompt you can paste directly into Cursor or Claude Code
5. **Learns** from your feedback — skip, accept, or tweak suggestions to get better advice over time

Not generic "add tests" noise. Each suggestion references specific issues, README promises, or commit history from your actual project.

## Install

```bash
claude install-skill https://github.com/Bobliew/nudge
```

Or manually:

```bash
git clone https://github.com/Bobliew/nudge.git ~/.claude/skills/nudge
```

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code)
- [GitHub CLI](https://cli.github.com/) (`gh auth login`)

## Usage

```
/nudge              # Scan your repos, pick one, get a suggestion
/nudge my-repo      # Get a suggestion for a specific repo
/nudge preview      # Preview without posting an issue
/nudge status       # See recent suggestions and stats
```

## How the advice gets better over time

When you accept, skip, or tweak a suggestion, nudge remembers your preferences per repo. Say "I prefer bug fixes for this project" or "too vague" — next time, the advice adjusts.

All data stays local in `~/.nudge/`.

## Philosophy

- You propose **what** to build. The developer decides **how**.
- Every suggestion must be completable in **30 min to 2 hours**.
- Evidence-based: references specific issues, README sections, or commits.
- No shaming. No "you haven't touched this in 8 months." The project is alive.
- No generic advice. "Add tests" and "improve docs" are banned.

## Privacy

- All state stored locally (`~/.nudge/`)
- API calls only to GitHub (via `gh`) and Anthropic (via Claude Code)
- No telemetry, no analytics, no accounts, no servers

## License

MIT
