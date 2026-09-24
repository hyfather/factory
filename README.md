# Software Factory

Why pay for yet another software factory? If you have a Claude Pro, Max, Team, or
Enterprise plan, you already have one! You just need to set it up.

This repo is basically one file, `RUN.md`. Drop it into your project, point a Claude Code
routine at it, and Claude will keep working on your project on a schedule. Every run
picks one thing to do, does it, opens a PR, merges it once CI passes, and leaves notes
for the next run.

## Setup

**1. Add the factory to your project**

From the root of your project, download RUN.md from the latest release:

```bash
mkdir factory
curl -L https://github.com/hyfather/factory/files/releases/latest/download/RUN.md -o factory/RUN.md
```


**2. Tell it what to build**

Put your goals in `factory/VISION.md`. Something like:

```
A Slack clone that I can use for chat with 3 agents that are each running on a
sandbox compute. Must support username/password auth.

Tech stack: Next.js, Neon, Vercel
```

Commit both files and push to `main`. 

**3. Create the routine**

Go to https://claude.ai/code/routines/new, pick your repo and an hourly schedule, and use this as
the prompt:

```
Carry out the instructions in factory/RUN.md.
```

## What happens on each run

Factory runs have a shared memory. The factory keeps its own notes in `factory/` and reads
them at the start of every run:

| File | What it's for |
|---|---|
| `RUN.md` | The instructions. The factory never edits this. |
| `VISION.md` | Your goals. The factory never edits this either. |
| `TODO.md` | The backlog, top item first. |
| `LOG.md` | What each run did. |
| `METRICS.md` | Run count, current status, when the next check-in is due. |
| `MEMORY.md` | Stuff future runs should know about. |
| `INBOX.md` | Questions for you, and your answers. |

You only need to write VISION.md. The factory creates the rest on its first few runs.

Each run does exactly one of these, in this order of priority:

1. Fix whatever's broken (failing build, stuck PR, leftovers from a crashed run)
2. Act on anything you answered in the inbox
3. Ask you a question, if it's really stuck
4. Write a check-in report (every 10 runs to start)
5. Plan the next 3 tasks, if the backlog is empty
6. Build the top task
7. Nothing, if there's nothing to do

## Talking to it

It won't ping you much. You'll hear from it when it has a question, when a check-in is
due, or when something breaks that it can't fix on its own.

To reply, write your answer under the question in `factory/INBOX.md` and push. The next
run will pick it up. If you want to change direction, edit VISION.md or shuffle TODO.md
around.

## What it won't do

It won't touch RUN.md or VISION.md, commit straight to `main`, force-push, delete branches
it didn't create, merge anything with failing CI, or turn off tests to make a build pass.

## A few tips

Get CI running early. The factory only merges when CI is green, so your tests are the
main thing keeping it honest. If you don't have CI yet, add it to your vision and the
factory will set it up. I found it easiest to get this working on Vercel.

Run it hourly for the first day or so and actually read the PRs. Once you're happy with
what it's producing, slow the schedule down.

The more specific your vision, the better. "Build a Slack clone" gets you guesses. Naming
the features and the stack gets you a plan.

LOG.md is the quickest way to catch up on what it's been up to.