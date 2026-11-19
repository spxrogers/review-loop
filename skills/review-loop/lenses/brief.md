# What every lens brief shares

A brief is assembled in this order (SKILL.md, Step 1): the header
below, the lens's own template, the report contract, the read-only
clause. A lens resumed for a later round gets the resume message
instead (SKILL.md, Step 4), which closes with the same report
contract and read-only clause.

## The header

Each of the four lens templates (the files beside this one) starts
from this header; fill the placeholders and keep it verbatim
otherwise:

> You are doing a <LENS> review of <PR>. The repo is at <path>;
> review commit <sha> on branch <branch>. The whole PR is
> `git diff <base>..<sha>`.
> <A whole-PR round:> **This is a whole-PR round: review the
> whole PR.** <From round 2:> This round's change is
> `git diff <previous-sha>..<sha>`: validate those fixes first,
> then read the whole change again.
> <A diff-only round:> **This is a diff-only round: review this
> round's change only,** `git diff <previous-sha>..<sha>` — the
> fixes, and what they reach (their callers, their tests, the
> prose they make false). The whole-PR diff is context for reading
> a fix, not ground to search again: earlier rounds reviewed it.
> If, while tracing a fix, you meet a defect in the PR's original
> change, report it marked *missed by the whole-PR rounds*; do not
> go looking for one.
> <From round 2: a few lines on what this round's commit changed
> and fixed.>
>
> **Read first:** the context file at <context-file> — what the PR
> does, decisions already made, how to run the checks, any shared
> services you must not touch, and the Round history. A decision
> listed there without the user's words or a link to them is the
> main session's own call: challenge it if you think it wrong. Then
> <the path of `severity-rubric.md`, which sits beside the skill's
> `SKILL.md`>, which is how you rate what you find; where the
> repo's review guides grade an area themselves, that grading
> comes first.
>
> Then <the repo's review guides the context file lists for every
> lens and for this one, each by path — leave this paragraph out
> when the repo has none>.
> The guides' angles are part of your lens, not background: a
> finding one of them names cites it (file and heading), and an
> angle there that applies to this diff and that you did not check
> is a gap in your review.
>
> The Round history lists findings the main session overruled, with
> its evidence. For each one your lens raised, check that evidence
> and answer it in one line: raise it again and say why the
> evidence is wrong, or drop it. Answer each finding of your lens
> filed as *out of scope* the same way, against the chain recorded
> for it: raise it again only to show the PR causes or worsens the
> defect. Answer a finding you marked *pre-existing, worsened by
> the PR* that was filed as plain pre-existing the same way.
> Findings the user deferred are
> listed there too, with the user's words, and so are NITs *held
> for the exit* and pre-existing findings already recorded: do not
> raise any of those again.

## The resume message

A later round sends each lens this instead of a brief, when the lens
is resumed rather than started fresh (SKILL.md, Step 4). Fill the
placeholders, take the round-kind lines from the header above
verbatim, and keep the rest verbatim otherwise; then append the
report contract and the read-only clause, as a brief does:

> Round <n> of the <LENS> review of <PR>: the main session has
> pushed fixes. If you no longer have your first brief for this
> review (your lens's angles, the context file, the guides it
> named), say so on the first line of your reply and stop there.
>
> Review commit <sha> on branch <branch>. The whole PR is
> `git diff <base>..<sha>`.
> <The header's round-kind lines for this round: from "This is a
> whole-PR round" or "This is a diff-only round" up to, not
> including, its "From round 2" placeholder.>
> <A whole-PR round:> Read the whole change again as if you had
> not: what you missed last round is what this pass is for.
> <A few lines on what this round's commit changed and fixed.>
> <After a rebase onto a fix's own PR since your last round:> This
> branch was rebased onto <base> since your last round, so the
> commit you last reviewed is no longer an ancestor; <previous-sha>
> is its rewritten equivalent. Diff only from the SHAs named here,
> never from one you remember. <What resolving a conflict changed,
> when one was needed.>
>
> **Everything you read earlier was at an older commit, and the
> tree has changed since.** Re-read at <sha> every file you cite,
> and every line a finding of yours rests on; a finding taken from
> your memory of the old tree is stale. Make any throwaway clone
> afresh at <sha>: one from an earlier round holds the old tree.
>
> **Then read the Round history** in the context file at
> <context-file>, round <n-1> in full, and every entry for a
> finding your lens raised. For each finding of yours that was
> fixed, check that the fix does what you meant, not only what the
> entry says. Answer each overrule of your findings, each one filed
> as *out of scope* and each *pre-existing, worsened by the PR*
> filed as plain pre-existing, as your first brief said: raise it
> again and say why the evidence is wrong, or drop it. Do not raise
> again what the user deferred, a NIT *held for the exit*, or a
> pre-existing finding already recorded. Your guides and the
> severity rubric are the ones your first brief named; re-read one
> when a finding turns on it. <Anything added to the context file
> since your last round — a decision, a review guide — by section
> or path:> Read it; it applies to you as if your first brief had
> named it.

## The report contract

Each brief closes with the same report contract; `<cap>` is the lens's
word cap, from its file:

> Report a ranked punch-list, most severe first, each item with
> file:line and a severity (`BLOCKER` / `ISSUE` / `NIT`). Rate each
> by the severity rubric you read first: for every finding above
> `NIT`, its description is its scenario, its consequence, who can
> produce it, and why it is not one label lower. Whether and when it
> is fixed is decided by the main
> session, not by you, so recommend no deferrals or follow-ups.
>
> **One finding per class, not per instance.** When a finding is
> one case of a mistake that could be repeated elsewhere in this
> change, report it once: name the class, list the instances you
> saw, and give the search that finds the rest (a `grep` pattern,
> the call sites of a function).
>
> **Mark a finding *pre-existing*** when the defect is in code this
> PR did not change and the PR neither makes it worse nor makes it
> more likely. Rate it as usual; say in a clause how you know the PR
> did not touch it. When the PR does make it worse or more likely
> (a new caller reaches it, a new input makes it fire), mark it
> *pre-existing, worsened by the PR*, and say how.
>
> **At most three NITs**, the three most worth a reader's time, and
> one line saying how many more you left out. If
> you have nothing real, list no findings and make the last line
> of your report exactly `CLEAN — ship it`. Don't manufacture
> findings. Stay under <cap> words — a ceiling, not a target.

## The read-only clause — append to every brief

Paste this verbatim at the end of each of the four briefs:

> **CRITICAL — you are READ-ONLY. Change nothing in the worktree.**
> Do not edit, create, or delete any file, and do not run
> `git checkout` / `git restore` / `git stash` — the main session
> has uncommitted work in this tree, and reverting a file you did
> not write destroys it.
>
> You may freely READ and RUN: `git log`/`show`/`diff`, `grep`,
> builds, tests, linters. If a finding needs a mutation measured
> (delete this line, invert that check — does any test fail?),
> either describe the mutation precisely in your report so the main
> session can run it, or measure it in a throwaway clone outside
> this worktree (`git clone . <scratchpad or /tmp>/probe-$$`) and say
> that you did.
>
> Before finishing, run `git status --porcelain` and confirm it is
> empty. State that in your report before its closing lines: a
> report that ends in a verdict (the CLEAN signal, a judge's verdict
> line) keeps that verdict as its last line. If
> it is NOT empty, do not
> "fix" it — say exactly what you saw; it is probably the main
> session's in-flight work.

Two reasons this is a paste-in clause rather than a summary:
`general-purpose` agents have write tools and will use them to be
helpful, and a reviewer that reports "tree clean" gives the main
session a cheap cross-check that costs one line.
