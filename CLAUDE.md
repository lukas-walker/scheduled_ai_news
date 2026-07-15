# scheduled_ai_news

This repo produces one AI news digest per day. It is run unsupervised on a
daily schedule (and can also be triggered manually for testing) — there is no
human present to answer questions or approve tool calls during a run, so
never stop to ask for clarification or confirmation. Make a reasonable
judgment call and keep going.

## Task

On every run, do the following in order:

1. Read `SOURCES.md` — tab-separated columns `Name / Type / Topic focus /
   URL / Feed URL`. Feed URL is blank when no feed is known. This is the
   full list of sources to check.
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

## Fetch strategy

Many of these sites block or rate-limit plain scraping even when the content
is public. To get a real hit rate, follow this order for each source:

1. If `SOURCES.md` lists a Feed URL for that source, fetch the feed first —
   RSS/Atom feeds are far less likely to be blocked than the HTML page, even
   when the page itself 403s.
2. Otherwise fetch the page URL directly.
3. If a source's Type is marked `search-only` (currently Instagram and
   LinkedIn personal-profile sources — both block non-browser fetches by
   design), skip direct fetching entirely and go straight to step 4.
4. If the direct fetch/feed fails (403, timeout, bot-block, DNS failure) or
   the source is search-only, fall back to a `site:<domain>` web search for
   recent activity before concluding there's nothing new.
5. Only after all of the above log it under `## Issues`, and say specifically
   *why* it failed (dead domain, wrong URL, blocked, no recent post found) —
   that detail is what lets the URL get fixed later. If the URL itself looks
   wrong or dead (not just blocked), say so explicitly so `SOURCES.md` can be
   corrected.

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

Group items under whichever of these categories apply that day (skip any
category with nothing in it that day, and keep the category order below so
the file reads the same way every morning). If an item spans more than one
category, put it under the single best fit rather than duplicating it.

```markdown
# AI News — YYYY-MM-DD

## Frontier Models
- **<Short topic title>** — up to 3 sentences with the most important
  takeaway. [Source](https://...)

## Science & Research
- **<Short topic title>** — up to 3 sentences. [Source](https://...)

## Software Engineering
- **<Short topic title>** — up to 3 sentences. [Source](https://...)

## Ethics
- **<Short topic title>** — up to 3 sentences. [Source](https://...)

## Switzerland
- **<Short topic title>** — up to 3 sentences. [Source](https://...)

## Geopolitics & Security Policy
- **<Short topic title>** — up to 3 sentences. [Source](https://...)

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
