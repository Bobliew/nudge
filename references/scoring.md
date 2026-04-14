# Repo Scoring Algorithm

This is the deterministic scoring algorithm used in Step 2. Apply these rules mechanically — do not use LLM judgment to override scores.

## Signals and weights

| Signal | Weight | How to detect |
|---|---|---|
| New star in last 30 days | +3 | Compare `stargazerCount` with stargazer dates if available, or use as heuristic from repo activity |
| New fork in last 30 days | +3 | Check fork events or compare `forkCount` over time |
| Open issue from non-owner with no owner reply | +5 | `issues` where `author != owner` and no comment from owner |
| Last push 30–180 days ago | +2 | Calculate from `pushedAt` |
| Last push 180 days – 2 years ago | +1 | Calculate from `pushedAt` |
| Last push < 30 days ago | -5 | Recently active, doesn't need revival |
| Last push > 2 years ago | -2 | Too old, likely truly abandoned |
| Suggestion already posted to this repo in last 4 weeks | -10 | Check `state.json` runs |
| User closed previous suggestion without commenting | -3 | Check issue status via `gh issue view` |

## Calculating days since last push

```
days_since_push = (current_date - pushedAt) in days
```

- `days_since_push < 30` → -5 (too active)
- `30 <= days_since_push <= 180` → +2 (sweet spot)
- `180 < days_since_push <= 730` → +1 (worth a look)
- `days_since_push > 730` → -2 (probably too far gone)

## Community signals

To detect non-owner issues without owner reply:

```bash
# Get issues not authored by the repo owner
gh api repos/{owner}/{repo}/issues?state=open --jq '[.[] | select(.user.login != "{owner}")] | length'
```

For stars/forks in last 30 days, if the API data is not precise enough, use a simpler heuristic: if `stargazerCount > 0` and the repo has been pushed to in the last 6 months, award +1 instead of +3.

## Tie-breaking

If multiple repos have the same score, prefer:
1. Repos with more open community issues (external demand)
2. Repos with more stars (wider impact)
3. Repos pushed more recently (easier to resume)

## Edge cases

- If user has < 3 repos total, just score what exists
- If all repos score negative or zero, output: "All your projects look either very active or very old. No suggestions this week."
- Repos in the exclusion list (`~/.ai-pm/feedback.json` → `excluded_repos`) get score = -999 (never selected)
