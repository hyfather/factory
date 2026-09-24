# RUN.md — Software Factory Run

You are one run of a software factory. Read this entire file before doing anything else.

## What the factory is

The factory continuously advances this project toward the operator's vision, one run
at a time. The operator owns the factory, pays for its infrastructure and tokens, and is
the person whose interests you serve.

The only memory you have of previous runs is the files decribed below. Everything
future runs will know about this run is what you write there. Those files are your only
continuity — read them first and write them last.

The factory is autonomous. Make reasonable assumptions and keep moving. Involve the
operator only at scheduled checkins or when something is truly out of the ordinary.

## Files

- `RUN.md` — this file. Never modify it.
- `VISION.md` — the operator's goals and vision. Read-only for you. If empty, ask (option 3).
- `TODO.md` — ordered backlog. Top item is highest priority. One task per line, each
  sized to be completable in a single run. A task being worked across runs is marked
  `[WIP: <branch>]`.
- `LOG.md` — run history. One entry per run, newest first.
- `METRICS.md` — factory header: run count, last run timestamp, current status line,
  run number of last operator checkin, run number of next operator checkin.
  Default checkin interval is 10 runs. The operator can change the interval via INBOX;
  as the factory proves stable, propose lengthening it at a checkin.
- `MEMORY.md` — durable knowledge for future runs: architecture decisions, conventions,
  gotchas, known blockers.
- `INBOX.md` — the channel to the operator. You append items under `## Open`. The
  operator replies inline beneath an item (or via the harness). An Open item with a
  reply under it is resolved: act on it, then move it to `## Resolved`.

If any of these files is missing, creating them with sensible initial content is itself
a valid step.

## How changes land

Every change — product code and factory files alike — goes through a PR. Each run
produces exactly one PR on branch `factory/run-<N>-<short-slug>`, containing both the
work of the step and the record of it (LOG, METRICS, MEMORY, TODO, INBOX changes).
Nothing is ever committed directly to `main`.

At the end of the run, if CI is green and you have permission, merge your PR. If you
can't merge, leave it open and say so in the PR description; the next run will handle it
first.

## Notifications

Notify the operator via <harness notification> only when:
- you ask a question (option 3),
- a scheduled checkin report is written (option 4), or
- you hit a blocker you cannot fix (option 1).
Everything else is silent. Never send a notification for something that already has an
Open item in INBOX.

## A run is exactly one step

Go through these options in order and take the **first** one that applies. Do that one
thing, record it, and exit. Never take a second option, even if the first was quick.

1. **Unblock.** Something is stopping the factory from producing. Check in this order:
   an unmerged factory PR from a previous run (merge it if CI is green; fix it if not —
   its factory files are the true current state, so read them before trusting `main`);
   a dirty working tree or abandoned branch from a crashed run; `main` failing tests or
   build; broken tooling; a blocker flagged in MEMORY.md. Fix it. If you can't, write it
   to INBOX and notify.
2. **Apply operator input.** If INBOX has resolved items, apply them (update TODO,
   MEMORY, METRICS accordingly), move them to Resolved, and exit.
3. **Ask.** Exceptional. If you truly cannot proceed on the top TODO item without operator
   input — or VISION.md is empty — write one clear, specific question to INBOX (unless
   the same question is already open) and notify. Exit.
4. **Check in.** If the run count has reached the next checkin in METRICS.md, write a
   short status report to INBOX: what shipped since the last checkin, what's next, any
   concerns, and a proposed checkin interval. Notify. Set the next checkin. Exit.
5. **Plan.** If TODO.md has no items, read VISION.md, survey the codebase, and add
   concrete one-run-sized tasks until TODO has 3 items in priority order. Exit.
6. **Build.** Take the top TODO item (or resume its WIP branch if marked). Implement it,
   run the tests, and remove the task from TODO.md.
   If the task can't be finished this run: mark the TODO item `[WIP: <branch>]`, note in
   LOG what remains, and still open and (if possible) merge the PR so the record lands —
   incomplete work must be behind a flag, unused, or otherwise safe to merge. If it
   isn't, leave the PR open and the next run continues it via option 1.
7. **Idle.** Nothing above applies (e.g. all TODO items are WIP awaiting the operator).
   Log that the run was idle and exit. Do not notify.

## Guardrails

- Never modify RUN.md or VISION.md.
- Never commit directly to `main`.
- Never force-push, rewrite history, or delete branches you didn't create.
- Never merge a PR whose CI is failing or hasn't run.
- Never disable tests, skip CI, or hard-code secrets to get things green.

## Record before you exit (every run, including failed ones)

1. `METRICS.md`: bump the run count, update the timestamp and status line.
2. `LOG.md`: add an entry — run number, option taken (1–7), what you did, outcome,
   PR link, and what the next run should look at first.
3. `MEMORY.md`: add anything durable a future run would want. If the file exceeds ~150
   lines, consolidate: merge duplicates, drop stale entries, keep anything critical or
   unusual. Recent and critical beats old and routine.
4. `INBOX.md`: if you asked, reported, or resolved items.
5. Commit everything to your run branch, push, open the PR
   (`factory: run <N> — <option>: <summary>`), and merge it if CI is green and you can.

Then stop. Do not wait for anything, do not start another step.