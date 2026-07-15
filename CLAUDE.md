# scheduled_ai_news

This repo produces one AI news digest per day. It is run unsupervised on a
daily schedule (and can also be triggered manually for testing) — there is no
human present to answer questions or approve tool calls during a run, so
never stop to ask for clarification or confirmation. Make a reasonable
judgment call and keep going.

## Task

On every run, do the following in order:

1. Read `SOURCES.md` — tab-separated columns `Name / Type / Topic focus /
   URL`. This is the full list of sources to check.
2. Read the daily files already in `daily_news/` from the last 7 calendar
   days (fewer if the project is younger) to see what has already been
   reported.
3. Visit every source in `SOURCES.md` and look for content published since
   the last time it was checked (use the most recent daily file that
   mentioned it, or the last 7 days if unknown). This includes newsletters,
   blogs, research pages, podcasts, YouTube channels, Instagram profiles, and
   newspapers.
4. Keep only items relevant to: AI ethics, AI science, software engineering,
   frontier model advances, AI developments in Switzerland, and geopolitical
   or security-policy implications of AI.
5. Pick the 5-10 most interesting/important items across all sources.
6. Write **one new file** `daily_news/YYYY-MM-DD.md` (today's date) with
   those items — see Output format below.
7. Commit the new file and push to `origin/main` before finishing. A run
   that doesn't end in a push is incomplete.

## Deduplication rule

Before writing, check the last 7 files in `daily_news/`. Skip any story
already covered there. The only exception: if a previously covered story has
a material update (e.g. a follow-up development, a correction, a new
release), include only what's new and note which prior entry it updates.
Never re-report a story that hasn't changed just because a source resurfaces
it.

## Handling broken or unreachable sources

Sources will occasionally be dead, paywalled, rate-limited, or otherwise
unreadable (site gone, feed empty, video unavailable, login wall, etc.).
When that happens:

- Do not stop the run and do not ask what to do.
- Skip that source for today's items.
- Add one line about it under an `## Issues` section at the bottom of
  today's file, naming the source and the reason it failed.
- Continue with every other source as normal.

A run that produces zero usable news items should still commit a file
containing just the `## Issues` section — never skip the commit/push step.

## Output format

File path: `daily_news/YYYY-MM-DD.md`

```markdown
# AI News — YYYY-MM-DD

- **<Short topic title>** — up to 3 sentences with the most important
  takeaway. [Source](https://...)
- **<Short topic title>** — up to 3 sentences with the most important
  takeaway. [Source](https://...)

## Issues
- <Source name>: <why it couldn't be checked>
```

Omit the `## Issues` section entirely if every source was reachable.

## Git conventions

- Work directly on `main`, no branches or PRs.
- Commit message: `Daily AI news – YYYY-MM-DD`
- Stage only the new `daily_news/YYYY-MM-DD.md` file.
- Push immediately after committing, every run, without asking for
  confirmation — this repo's `.claude/settings.json` pre-approves the git
  and fetch commands this task needs so it can run unattended.

## Testing locally

To run a full cycle on demand (outside the schedule), just say something
like "do your thing" or "run today's digest now" — follow the exact same
steps above, including the commit and push at the end.
