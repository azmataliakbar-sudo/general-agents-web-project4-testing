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

## Actual Results

- We ran the real script (`monday_brief.py`) twice by hand on the local machine, and both runs wrote dated brief files into the `output/` folder. Two walks, two success signals, just as the design called for.
- Because waiting a whole week for the real Monday schedule was too slow, we built a separate **fast-test copy** at `test/monday_brief2.py`. Same logic, same live data, but it writes to `test/output/test-brief-<timestamp>.md` so it never overwrites the real files. This way we could see proof in minutes instead of days.
- We pushed the project to GitHub at: **https://github.com/azmataliakbar-sudo/general-agents-web-project4-testing**
- Along the way we had to fix two real problems:
  - **Wrong saved GitHub login** — the first push was rejected with a `403` error because Git was using an old, unauthorized account. We fixed it by signing in with the correct account.
  - **Missing write permissions** — the scheduled workflow could read the repo but could not push new files back. We fixed this in GitHub under **Settings → Actions → General → Workflow permissions → "Read and write permissions"**.
- After those fixes, the fast test workflow ran on GitHub's own server and created **`test/output/test-brief-2026-09-07-104810.md`** automatically, with fresh live data from GitHub trending and Hacker News. This is the proof that the schedule actually fires by itself, with no computer of ours turned on.
- Once we had proof, we **disabled the 10-minute auto-schedule** in `test-schedule.yml` (the `schedule:` block is now commented out) to stop burning GitHub Actions minutes. The `workflow_dispatch` (manual) trigger was left on so we can re-run the test on demand.

Proof file: `test/output/test-brief-2026-09-07-104810.md` — created by GitHub Actions, confirmed by git pull to local machine.