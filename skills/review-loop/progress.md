# What the user sees while the loop runs

A run of several rounds is long, and the user follows it in the conversation, often on a
phone. This file sets the three things the loop shows them, so each round reads the same way
and the decisions that change the run's course stand out: the **round table** after every
round's audit, the **judge's verdict** when Step 6 runs, and the **recap** in the final
report ([`report.md`](report.md)).

## Severity colours

Wherever a severity is shown to the user (the conversation, the PR comment), it carries its
colour:

| Shown as | Label |
|---|---|
| 🔴 BLOCKER | `BLOCKER` |
| 🟠 ISSUE | `ISSUE` |
| 🟡 NIT | `NIT` |

Briefs, lens reports, the context file's Round history and the scorecard keep the plain labels:
they are read by agents and scripts, and searched as text.

## The round table

Shown once per round, when its audit is done (Step 2), before any fix is made, and saved
beside the context file as `round-<n>-table.md`. The final report's recap is copied from those
files, never rebuilt from memory: the conversation may have been compacted by then.

````markdown
### Round 2 of 3 · whole PR · `a1b2c3d`

🔴 0 · 🟠 2 · 🟡 3 — not converged

| Severity | Finding | Verdict |
|---|---|---|
| **correctness** | | |
| 🟠 ISSUE | `parse.ts:88` retry loses the cursor on a 429 | ✅ Accept |
| 🟡 NIT | Stale comment above `flush()` | ✅ Accept · exit |
| **adversarial** | | |
| 🟠 ISSUE | Unescaped `title` in the export header ×3 (+ correctness) | ✅ Accept |
| 🟡 NIT ← 🟠 | Log line can exceed 60 KB | ✅ Accept — rated down: field capped at parse |
| **api-design** — CLEAN | | |
| **test-rigor** | | |
| 🟠 ISSUE | Timeout test passes with the timeout dropped | ❌ Reject — `wrapper.test.ts:40` pins it |
| 🟡 NIT | Duplicated fixture helpers | ✅ Accept → filed: out of scope |
````

**The heading** names the round, the budget, the kind of round (`whole PR`, `diff only`, or
`extension 1 of 2 · judge PASS`, or `granted by owner`) and the short SHA the lenses reviewed.
**The tally under it** counts the audited BLOCKERs, ISSUEs and NITs of the round's rows, a
rejected row not included, and says how the round ends: `converged`, `not converged`, or
`round gate — fixes held`.

**Rows are grouped by lens**, in the order correctness, adversarial, api-design, test-rigor. A
lens's group opens with a row holding only its name in bold. A lens with no findings is one row,
`**<lens>** — CLEAN`. Within a group, rows run from most severe to least; at equal severity a
finding more than one lens raised comes first.

**A row is one Round history entry**: one finding, or one class with all its instances. So:

- **A class** is one row, its count in the summary (`×3`), never one row per instance.
- **A finding several lenses raised** is one row, in the group of the first of them in the order
  above, with the others named at the end of the summary (`(+ correctness)`).
- **A finding a lens raises again** in a later round is not a new row in that round: the
  earlier round's row stands. A repeat that changes the verdict (a withdrawn overrule, the
  lens's answer to a filed finding) is a row again, its verdict saying what changed.

**Severity** is your audited rating. When it differs from the lens's, the lens's follows an
arrow: `🟡 NIT ← 🟠`. A row rejected as false keeps the lens's own label, and sorts by it.

**Finding** is one line: the file name and line when there is one (not the full path), and
what is wrong, in a dozen words or so. The scenario, the evidence and the lens's words stay in
the Round history; the table is how the user scans the round, and it has to fit a phone.

**Verdict** says first whether you accept the finding as real, then, after it, what happens to
it:

| Verdict | When |
|---|---|
| `✅ Accept` | a BLOCKER or ISSUE fixed in this round's commit |
| `✅ Accept · held` | a BLOCKER or ISSUE held at the round gate (Step 6) |
| `✅ Accept · unverified` | accepted with the audit skipped (Step 2, item 5) — a lapse, and shown as one |
| `✅ Accept · exit` | a NIT held for the exit (Step 3) |
| `✅ Accept — rated down: <reason>` | true, but rated below the lens's label: an overrule (Step 2, item 4) |
| `✅ Accept → filed: pre-existing` | filed (Step 2, item 6) |
| `✅ Accept → filed: out of scope` | filed, the chain in the Round history |
| `✅ Accept → own PR #<n>` | pre-existing, worsened by the PR, fixed in its own PR (Step 3) |
| `⏸ Awaiting you — <question>` | a proposed deferral, a disagreement the mandate leaves to the user, or a finding neither of you can verify |
| `❌ Reject — <reason>` | dropped as false: an overrule. The reason is the evidence, in a few words |

A deferral the user grants later changes the row's verdict to `↪ Deferred by you` in the saved
table, and in the next round's table as a row of its own.

## The judge's verdict

When Step 6 runs, the post-mortem and the judge decide the rest of the run, and in a long
conversation they are easy to scroll past. So the verdict is shown on its own, as soon as it
arrives, before anything else is done with it, set apart by a rule above and below:

````markdown
---

## ⚖️ Judge: PASS — two more rounds granted (rounds 4–5)

> <the judge's reasoning, in its own words, two to four lines>

- **Post-mortem honest:** yes — <one clause>
- **Path reaches Convergence in two rounds:** yes — <one clause>
- **Gated areas on the path:** none

**Next:** applying the path's fixes to <files>, then round 4.

---
````

For the other verdicts the heading says what happens to the run, and the three lines say which
test failed:

- `## ⚖️ Judge: FAIL — the run stops; report to the owner follows`
- `## ⚖️ Judge: GATED — <areas>; the run stops; report to the owner follows`
- `## ⚖️ Judge: no verdict — the run stops; report to the owner follows`

When the safety check stops the run before a judge is launched (Step 6.2), the same block is
shown with the heading `## ⛔ Round gate: <gated areas, or awaiting your answer> — no judge;
report to the owner follows`.

After a PASS, every extension round's table heading carries `extension <k> of 2 · judge PASS`,
so the reason the run is still going is on the screen at every round. Every final report of a
run in which the judge ran repeats the verdict ([`report.md`](report.md), *The judge*).
