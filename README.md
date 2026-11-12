# review-loop

A multi-round code review skill for coding agents. Four reviewers, each with its own lens
(correctness, adversarial, API design, test rigor), read a pull request in parallel. The main
session checks what they found, fixes what holds up, and runs another round, until a round turns
up nothing above a nit or the budget of rounds runs out.

It is written for Claude Code and works in any agent that loads skills in the `SKILL.md` format
and can start subagents.

## What it does

- **Reviewers are read-only.** They report; the main session is the only one that edits the
  worktree.
- **The main session audits every finding** before acting on it, and rates it by a written
  rubric: `BLOCKER`, `ISSUE` or `NIT`.
- **It stops at convergence.** A round with no `BLOCKER` and no `ISSUE` in any lens ends the
  loop. A `NIT` never buys another round.
- **The budget is 3 rounds.** If it runs out, the loop writes a post-mortem, and either a judge
  grants two more rounds or the loop reports to the person who owns the decision.
- **Later rounds review the fixes.** The first two rounds read the whole PR; after that a round
  reads only what the last round changed.
- **It ends with a report** in a fixed format, posted as a comment on the PR.

It is heavy: 12 subagent runs by default, up to 21. It is meant for substantive changes (new
interfaces, contract changes, load-bearing refactors), not small fixes or doc-only PRs.

## Layout

```
skills/review-loop/
  SKILL.md             the method
  lenses/              one file per lens, and brief.md for what every brief shares
  severity-rubric.md   what BLOCKER, ISSUE and NIT mean, and how to choose
  report.md            the final report's format
reviewlooprc.example   a starting point for a repo's own .reviewlooprc
```

## Install

Copy `skills/review-loop/` into the place your agent loads skills from. For Claude Code in one
repository:

```bash
git clone https://github.com/spxrogers/review-loop
mkdir -p .claude/skills
cp -r review-loop/skills/review-loop .claude/skills/review-loop
```

Then ask for it by name: `/review-loop`, with the PR and what you want from the review. The
skill does not start on its own from a plain "review this".

## Your repo's conventions: `.reviewlooprc`

The skill names no repository and no stack. What your repo adds goes in one prose file at the
repo root, `.reviewlooprc`, which the skill reads before anything else when it exists. Each
section is optional:

| Section | What it holds |
|---|---|
| *Read first* | your review guides, and how to pick the ones a diff calls for |
| *Checks and shared services* | how to run the checks, and what a reviewer must not touch |
| *Lens angles* | extra angles for each lens, in your stack's terms |
| *Fixing* | rules a fix must follow, and the prose a fix can make false |
| *Gated areas* | what each gated area means in your repo, and any area you add |
| *Reporting* | what you require of a report or of follow-up work |

The file adds to the skill; it cannot loosen it. `reviewlooprc.example` has the headings to
start from.
