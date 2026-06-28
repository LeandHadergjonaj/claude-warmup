# claude-warmup

A scheduled GitHub Action that sends Claude Code one throwaway prompt on a cron
schedule. The point is purely **timing**: it starts your 5-hour usage window
before you sit down to work, so the window *resets mid-workday* instead of right
after you stop for the day.

## What it does

- On a cron schedule (and on manual trigger), spins up a runner, installs
  Claude Code, and sends a single prompt: `warmup ping, reply with ok`.
- That one prompt is enough to open a fresh 5-hour usage window at a time you
  choose.

## What it does NOT do

- It does **not** increase your usage or limits. It sends exactly one tiny
  prompt per run and never loops real work.
- It does **nothing** for the weekly cap — that's a total-usage limit, and this
  tool doesn't change total usage.
- It only controls **when** the rolling 5-hour window starts. That's it.

## Setup

1. **Create the repo.** Put these files in a GitHub repository (public or
   private both work).
2. **Get an OAuth token.** Run `claude setup-token` locally. It produces a
   token that starts with `sk-ant-oat01-...`.
3. **Add the secret.** In the repo: **Settings → Secrets and variables →
   Actions → New repository secret**. Name it exactly
   `CLAUDE_CODE_OAUTH_TOKEN` and paste the token as the value.
4. **Edit the cron (in UTC).** The schedule in
   `.github/workflows/warmup.yml` is in **UTC**, not your local time. Adjust the
   times so the runs land when you want your windows to open. Use
   [crontab.guru](https://crontab.guru) to check your expression. A commented-out
   every-~5-hours alternative is included in the workflow.
5. **Test it.** Go to the **Actions** tab, pick the **warmup** workflow, and use
   **Run workflow** (workflow_dispatch) to trigger a manual run and confirm it
   succeeds.

## Gotchas

- **Scheduled runs drift.** GitHub Actions cron is best-effort. Under load,
  scheduled runs can start late (sometimes by many minutes), so don't count on
  exact timing.
- **GitHub disables idle schedules.** Scheduled workflows are automatically
  disabled after **60 days** with no commit activity in the repo. Push a commit
  (or re-enable the workflow) periodically to keep it alive.
