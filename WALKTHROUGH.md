# Project 4 — Walkthrough & Schedule Setup

## Six Answers Recap

| Q | Answer |
|---|---------|
| Trigger | Monday 09:00 UTC (cron `0 9 * * 1`) |
| Touch | `api.github.com`, `hacker-news.firebaseio.com` (public, auth-free HTTPS) |
| Device independence | All public internet; works with laptop off |
| Success signal | `output/monday-brief-YYYY-MM-DD.md` exists, > 0 bytes, with both sections |
| Autonomy | Read-only fetch + local file write. No sends, no mutations, no deletions. |
| Empty case | If API fails, write section with `> No data available` and the captured error |

## Cloud-Reachable Sources

- **GitHub Search API** — `https://api.github.com/search/repositories?q=created:>YYYY-MM-DD&sort=stars`
  - No auth, public endpoint
- **Hacker News API** — `https://hacker-news.firebaseio.com/v0/topstories.json`
  - No auth, public endpoint

## Walk #1 (manual)

```bash
cd C:\Projects\general_agents_web\project_4
python monday_brief.py
```

Expected: `output/monday-brief-YYYY-MM-DD.md` written with both sections populated.

## Walk #2 (manual, second invocation)

```bash
cd C:\Projects\general_agents_web\project_4
python monday_brief.py
```

Expected: another dated file written; success signal confirmed in stdout.

## Schedule (Cloud / Device-Independent)

The cron daemon on this machine is local. For true cloud-independence, this would run on:

- A GitHub Action with `schedule:` cron (`- name: Generate brief` weekly Mon)
- A serverless function with a scheduled trigger (Vercel Cron, Cloudflare Cron, AWS EventBridge)
- The platform's own Scheduled Tasks feature (ScheduleWakeup / CronCreate only run while the session is alive — they are **not** device-independent).

**Important:** `CronCreate` in Claude Code is session-scoped (dies with the session). For Project 4's "computer off" requirement we need a real cloud trigger. The local cron on Windows is a *training-wheels* step only.

### Local schedule (training wheels — Windows Task Scheduler)

```powershell
$action = New-ScheduledTaskAction `
  -Execute "python" `
  -Argument "C:\Projects\general_agents_web\project_4\monday_brief.py"
$trigger = New-ScheduledTaskTrigger -Weekly -DaysOfWeek Monday -At 09:00
Register-ScheduledTask -TaskName "MondayBrief" -Action $action -Trigger $trigger
```

### Cloud schedule (true device-independent, preferred)

A GitHub Actions workflow at `.github/workflows/monday-brief.yml` runs every Monday 09:00 UTC, executes the same script on a runner, and commits the result back. With the laptop off, the runner still runs.

## Done When

- [x] Two manual runs produced two dated brief files
- [ ] Cloud schedule has fired at least twice with computer off
- [ ] Each fired run left a verifiable success signal in tier 3 (the dated file)
