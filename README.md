# Project 4: The First Cloud Schedule

## Six Design Answers

| Question | Answer |
|----------|--------|
| **Trigger** | Clock schedule — every Monday at 09:00 UTC |
| **Touch** | GitHub REST API (public, no auth), Hacker News API (public) |
| **Device Independence** | Yes — only public HTTPS endpoints, no local connectors needed |
| **Success Signal** | Dated Markdown file written to `output/` directory (tier 3) with brief content |
| **Autonomy** | Read-only: fetch public data, generate report, write file. No mutations, sends, or deletions |
| **Empty/Ambiguous Case** | If APIs fail or return no data, write a brief noting "No data available" with timestamp and error summary |

## Workflow

1. Fetch GitHub trending repositories (created this week, sorted by stars)
2. Fetch top Hacker News stories
3. Generate a Monday brief Markdown file
4. Write to `output/monday-brief-YYYY-MM-DD.md` (tier 3)

## Verification

- Run manually twice to verify
- Schedule via cron
- Verify runs fire with computer off (check output files on next session)