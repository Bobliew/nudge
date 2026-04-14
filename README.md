# ai-pm

Your weekly AI product manager for forgotten side projects.

A [Claude Code](https://docs.anthropic.com/en/docs/claude-code) skill that scans your GitHub repos, picks the ones most worth reviving, and generates concrete actionable suggestions — posted as GitHub issues under your own identity.

## What it does

1. **Scans** all your GitHub repos via `gh` CLI
2. **Scores** each repo using signals like community issues, recent stars, and time since last commit
3. **Picks** the top 3 most worth reviving (deterministic, no LLM involved)
4. **Generates** one concrete, actionable suggestion per repo (this is where Claude shines)
5. **Posts** each suggestion as a GitHub issue with an agent-ready prompt included

The suggestions are product-level thinking, not generic "add tests" noise. Each one references specific evidence from your README, issues, and commit history.

## Install

### Option 1: Install as a skill

```bash
claude install-skill https://github.com/Bobliew/ai-pm
```

### Option 2: Manual install

Clone this repo into your Claude Code skills directory:

```bash
git clone https://github.com/Bobliew/ai-pm.git ~/.claude/skills/ai-pm
```

## Prerequisites

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) installed
- [GitHub CLI](https://cli.github.com/) installed and authenticated (`gh auth login`)

## Usage

```
/ai-pm              # Full run: scan → suggest → confirm → post issues
/ai-pm preview      # Preview suggestions without posting
/ai-pm status       # Show last run and stats
/ai-pm exclude repo # Exclude a repo from scanning
/ai-pm include repo # Re-include an excluded repo
/ai-pm reset        # Clear all state and feedback
```

## How it works

### Scoring (no LLM)

Repos are scored deterministically:

| Signal | Score |
|---|---|
| Open issue from non-owner, no reply | +5 |
| New star in last 30 days | +3 |
| New fork in last 30 days | +3 |
| Last commit 30-180 days ago | +2 |
| Last commit 180 days - 2 years | +1 |
| Recently active (< 30 days) | -5 |
| Very old (> 2 years) | -2 |
| Already suggested in last 4 weeks | -10 |

### Suggestions (Claude as PM)

For each selected repo, Claude reads the README, recent commits, open/closed issues, file tree, and manifest. If a local clone exists, it also reads TODO comments and unfinished branches.

Claude then thinks in two stages:
1. **Diagnosis** — what stage is this project in? what do users want?
2. **Suggestion** — what's the single highest-ROI thing to do next?

Each suggestion includes an agent prompt you can paste directly into Cursor or Claude Code.

### Feedback loop

When you accept, skip, or tweak suggestions, ai-pm remembers your preferences per repo. Over time, the suggestions get more aligned with what you actually care about.

## Data & privacy

- All data stays on your machine (`~/.ai-pm/`)
- API calls go only to GitHub (via `gh` CLI) and Anthropic (via Claude Code)
- No telemetry, no analytics, no accounts, no servers

## License

MIT
