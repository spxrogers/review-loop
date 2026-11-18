# Lens 5 — Simplicity

Cap: 600 words.

This lens runs in **round 1 only**: the first revision, where code
the change does not need is introduced. It never runs in a later
round, a diff-only round, an extension or a round the owner grants
(SKILL.md, Step 1). Later rounds still check the fixes: API design
for over-engineering, Correctness for a fix that guards a symptom.

> **Your job.** The change is the whole PR. Ask of it: **is this the
> simplest approach that correctly solves the problem at hand?**
>
> 1. **Should this exist?** For each new piece (a function, a type,
>    a file, an option, a check, a tool, a step), what caller,
>    requirement or recorded decision needs it now? Speculative
>    options and configuration, extension points nothing extends,
>    generality built for one caller.
>
> 2. **Reuse.** Does the language's standard library, the
>    platform's own API, a dependency the project already declares,
>    or a helper already in the repo do this as well as the change
>    needs?
>
> 3. **Duplication.** Is any of it a second copy of logic that
>    already exists?
>
> 4. **Indirection.** A layer, wrapper or interface with one
>    implementation, standing at no seam the repo requires.
>
> 5. **Proportion.** Is the mechanism sized to the problem? A tool,
>    a lint or a framework for something that happens once; a
>    general solution to a case the change has one of.
>
> **Every finding names the simpler alternative concretely, and
> what it deletes**: the files, functions or lines that go. Rate it
> by the severity rubric's ruling *A simpler alternative*: it is an
> `ISSUE` only when the change as built pays one of the costs that
> ruling names; "shorter" or "cleaner" alone is a `NIT`.
>
> **The carve-outs.** An alternative you propose must:
> - be **correct on every path the original handles**: its error
>   paths, its edge inputs, its concurrent case;
> - **never be a hack**: not a guard on a symptom whose cause stays
>   reachable (the rubric's *A fix that guards a symptom*), not a
>   dropped error path, not a weakened or skipped check, test or
>   assertion;
> - **never go against a recorded decision** (the context file lists
>   them) **or a seam the repo requires** (its rules or review guides
>   say where). Simpler is not a reason to reopen either.
>
> If the change is already the simplest correct approach, say
> nothing about simplicity: a lens with no findings is `CLEAN`.

**Repo additions.** If the repo's `.reviewlooprc` has a *Simplicity*
section under *Lens angles*, add its angles to the brief after this
template, under the line "This repo's own angles for your lens:"
(SKILL.md, Step 0).
