# The final report — a comment on the PR

Every report the loop makes when it ends (SKILL.md, Step 5: converged, a report to the owner at
the round gate or in the extension, the end of rounds the owner granted) is posted as **one new
comment on the PR**, and given in the conversation as well. It is never an edit to the PR body
or to an earlier report: a run that reports to the owner and later converges leaves two
comments, and the newest is the run's state.

The comment has a fixed first line and opens with a scorecard, so a script can find every run in
a repository and read how it went. Everything after the scorecard is for a person.

## The shape

````markdown
## review-loop report

```text
review-loop-scorecard v2
outcome: converged
head: <full sha the report describes>
rounds: 3
budget: 3
whole_pr_rounds: 2
extended: false
findings: 9
blocker: 1
issue: 5
nit: 3
verified: 5
accepted_unverified: 0
overruled: 1
pre_existing: 1
out_of_scope: 0
fixed_in_own_pr: 0
missed_by_whole_pr_rounds: 0
from_earlier_fix: 2
classes_swept: 2
by_lens: correctness=3 adversarial=2 api-design=1 test-rigor=4
```

<the report itself: SKILL.md, Step 5>
````

- **The first line is exactly `## review-loop report`**, with nothing before it. Whatever the
  session's own rules add to a comment (an attribution footer) goes at the end.
- **The scorecard is the first fenced block**, one `key: value` per line, every key present, in
  this order, a count of `0` written as `0`. Its first line names the format's version; a change
  to the keys or to what one counts raises it.
- **After it, the report Step 5 requires**, written for a reader with none of the run's context:
  how the run ended, then the sections below on the judge, then the rounds and the NITs fixed
  (at Convergence) or what holds the run open (at any other ending), then
  every finding left open (held, deferred, NITs not fixed), the section below on findings sent
  to another PR, every overrule of a BLOCKER or ISSUE with the lens's words beside the
  evidence, every one accepted without verification, the issues filed, and that the loop did
  not run the full checks on the head.
  When nothing is left open, say so in one line.
- **Severities carry their colours** everywhere after the scorecard: 🔴 BLOCKER, 🟠 ISSUE,
  🟡 NIT ([`progress.md`](progress.md)). The scorecard keeps plain text.

## The judge

When the run reached Step 6, a section headed `### The judge`, straight after the line on how
the run ended: the verdict line as the judge wrote it, its reasoning in its own words (two to
four lines), and what followed (the rounds it granted and whether they converged, or that the
run stopped). When the safety check stopped the run before a judge ran, the section is headed
`### The round gate` and names the gated areas, or the findings awaiting the user. A run that
never reached Step 6 has neither section.

## The rounds — at Convergence

When a round converges, whichever round it is (within the budget, in the extension, or in a
round the owner granted), a section headed `### Rounds`: every round's table
([`progress.md`](progress.md)), in order, copied from the saved `round-<n>-table.md` files,
**without its NIT rows**. A row is a NIT row when its severity column starts with 🟡: a finding
rated down to a NIT is dropped, and an ISSUE rejected as false stays, since it is an overrule.
A lens group left with no rows becomes the one row `**<lens>** — no BLOCKER or ISSUE`; a round
left with none at all keeps its heading and tally, and the line `No BLOCKER or ISSUE.` in place
of its table. Verdicts are brought up to date: a row held at a round gate and fixed later says
so (`✅ Accept · held → fixed in <sha>`).

## Holding the run open — at any other ending

When the run ends without converging (a report to the owner at the round gate, the extension or
the granted rounds run out, a gated fix in an extension), there is no per-round recap. Instead,
a section headed `### Holding the run open`: one table in the round table's shape
([`progress.md`](progress.md)), holding **only the BLOCKERs and ISSUEs still open** — the ones
that kept the last round from converging. That is a row whose verdict is `✅ Accept · held`,
`⏸ Awaiting you`, or `✅ Accept → own PR #<n>` not yet merged under this branch. Nothing else
goes in it: no NIT, no row already fixed, filed or deferred, no row rejected (overrules have
their own section). Its heading names the head SHA and the last round; a row raised in an
earlier round says which at the end of its summary (`(round 2)`). Rows are grouped by lens
and sorted most severe first, as in a round table; a lens holding nothing is left out, not
written CLEAN.

## NITs fixed on the way out

At Convergence only, a section headed `### NITs fixed on the way out`, after the rounds: one
bullet per NIT fixed in the final commit (Step 3's return test), from any round. Each is the
file and line, and what changed, in a few words:

```markdown
- `flush.ts:12` — stale comment above `flush()` now describes the batching
- `README.md:40` — install step names the current flag
```

When none passed the return test, the section is the one line
`### NITs fixed on the way out: none`. NITs left unfixed are listed with the open findings, not
here.

## Findings sent to another PR

A section headed `### Sent to another PR`, after the findings left open. It has three lists.
Each finding is one line: severity, lens(es), file:line, what is wrong, then what each list asks:

- **Pre-existing — filed.** Each with how the audit showed the PR did not touch it, and its
  issue (or the open or *not planned* issue it was linked to instead). NITs are listed with no
  issue; a NIT the PR worsened is listed here too, saying how.
- **Out of scope — filed.** Each with why: *clearly outside the PR*, or the chain its fix would
  set off (the files or modules beyond the PR). For a BLOCKER or ISSUE, the lens's own words
  beside that reason, and how the lens answered when it was told (or *not answered: no later
  round*); then its issue.
- **Fixed in their own PR.** Each pre-existing defect the PR made worse or more likely: how the
  PR worsened it, the fix's PR, and that this PR merges after it (or that it has merged). One
  *held* at the report is in the open findings instead, marked as bound for its own PR.

A list with nothing in it is written `none`, so a reader can tell an empty list from a missing
one. When all three are empty, the section is the one line `### Sent to another PR: none`.

## What each key counts

Count from the context file's Round history, never from memory. One *finding* is one entry
there: a class with five instances is one, and a finding two lenses raised is one.

| Key | Value |
|---|---|
| `outcome` | `converged`; `owner-report` (at the round gate: a gated path, a finding awaiting the user, a judge that did not pass; or a gated fix met in an extension or granted round); `extension-exhausted`; `granted-rounds-exhausted` |
| `head` | the commit pushed last, in full; at Convergence, the one carrying the NIT fixes |
| `rounds` | rounds whose reviewers reported, over the whole run so far |
| `budget` | the budget the run started with |
| `whole_pr_rounds` | the setting, not a count of rounds run: 2, or the number the user or a calling skill set |
| `extended` | `true` once the judge has granted the two extra rounds |
| `findings` | every entry, at its audited severity; one dropped as false is included at the severity the lens gave it |
| `blocker`, `issue`, `nit` | `findings` split by that severity; they sum to it |
| `verified` | BLOCKERs and ISSUEs, by the lens's label and neither filed as pre-existing nor out of scope (a worsened one counts here), accepted at that label or above with the line of evidence that showed them real |
| `accepted_unverified` | BLOCKERs and ISSUEs, neither pre-existing nor out of scope, accepted with the audit skipped |
| `overruled` | BLOCKERs and ISSUEs, neither pre-existing nor out of scope, dropped as false or rated below the lens's label, and not withdrawn; one rated down and then fixed counts here only |
| `pre_existing` | entries of any severity confirmed pre-existing and filed, a worsened NIT included; not a worsened BLOCKER or ISSUE |
| `out_of_scope` | entries of any severity filed as out of scope |
| `fixed_in_own_pr` | entries the PR worsened, fixed in their own PR; also counted in `verified` or `accepted_unverified` |
| `missed_by_whole_pr_rounds` | entries in the PR's original change first raised in a diff-only round |
| `from_earlier_fix` | BLOCKERs and ISSUEs introduced by an earlier round's fix |
| `classes_swept` | entries whose class was searched for, whether or not the search found more |
| `by_lens` | per lens, the entries it raised; a finding two lenses raised counts for both |

`verified`, `accepted_unverified` and `overruled` together are every finding a lens labelled
BLOCKER or ISSUE, apart from the pre-existing and out-of-scope ones and those still awaiting the
user. A high `overruled` for one lens says that lens is noisy; a high `from_earlier_fix` says the fixes are the problem;
a high `missed_by_whole_pr_rounds` says the whole-PR rounds are too few or too shallow.

## Finding the reports

Every issue and PR comment in a repository, filtered to the reports (GitHub's REST endpoint
`GET /repos/{owner}/{repo}/issues/comments`):

```bash
gh api "repos/{owner}/{repo}/issues/comments" --paginate \
  --jq '.[] | select(.body | startswith("## review-loop report")) | {pr: .issue_url, at: .created_at, body}'
```

One value across every run, here the rounds each took:

```bash
gh api "repos/{owner}/{repo}/issues/comments" --paginate \
  --jq '.[] | select(.body | startswith("## review-loop report")) | (.body | capture("\nrounds: (?<n>[0-9]+)").n)'
```
