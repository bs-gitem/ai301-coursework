# Procedure: how this tool grades a PR package

<!--
Written by Bailey Souza (bs-gitem) for AI301 Unit 4, 2026-10-14, with Claude Code as
the drafting assistant. Rewritten from my Unit 3 procedure for a PR package. The plan is
read before the diff, and the diff before the description, so the description's
confidence cannot decide what the diff "really" does (the calib-03 trap). Where a step
says "record", write the fact down before moving on; grades are built only from records.
-->

## Read order

1. Eval mode: read the package header (`source:`, `captured:`, `calibration:`). Live mode: read `scope.md` first; stop if the PR targets a repo outside it or the `Repo:` line is still a placeholder.
2. Read the **Repo facts** block (live: the repo's `.github/PULL_REQUEST_TEMPLATE.md`, `docs/CONTRIBUTING.md`, and the house rules in `scope.md`). Record verbatim: every template section or checklist the PR must fill, every artifact the repo requires a PR to carry (changelog, whatsnew, tests, marker removal), and the exact AI-policy wording.
3. Read the **Issue** and **Thread highlights**. Record in one sentence the trigger and the symptom, and any explicit maintainer direction.
4. Read the **Plan context** BEFORE the PR. Record: the in-scope work items, every file or function named, the not-in-scope line, every deferral or deviation note, and the test plan, listing each repro or failure mode it says will be re-run and the expected after outcome for each.
5. Read the **Diff** next, before the description. Record each changed file and, per hunk, one phrase saying what the hunk does. Read the **Commits** list with it.
6. Read the **Description** and **Title** after the diff. Record each factual claim it makes about the change, each deferral it states, the issue link, the template sections it fills, and any AI-use disclosure.
7. Read the **Test evidence** last (live: `test_evidence.md` plus the description's Testing section). Record each command or step run, the output it shows, and whether it is a before or an after.
8. Live mode only: after step 7, read `voice-guide.md` and hold the title and description against each rule; note broken rules for the summary. This never changes a grade.

## Evidence gathering

9. **Plan fidelity** (feeds plan-fidelity): put the hunk list (step 5) next to the plan's work items (step 4). Mark each hunk "named by the plan", "named by a deviation note", or "not named" and record every "not named" hunk with its file. Then mark each plan work item "in the diff", "deferred with a reason", or "missing" and record every "missing" item.
10. **Description against diff** (feeds description-honest): for each claim recorded in step 6, find the hunk that makes it true. Record every claim with no hunk behind it, and every fidelity claim ("exactly as planned", "no other changes") that sits over a "not named" hunk from step 9.
11. **Evidence against test plan** (feeds decisive-test-evidence): for each repro or failure mode in the plan's test plan (step 4), find the evidence run that re-executes it on the changed path and shows the expected after. Record each one as "shown with output", "result stated in words", or "not mentioned". Only the main repro needs "shown with output"; every other named item needs at least "result stated in words". Record whether the run exercises the fixed path or only a control or unchanged path. Record whether the repo's checks or the plan's named suite have a stated outcome.
12. **Debris scan** (feeds clean-diff): walk every `+` and `-` line of the diff. Record any debug print or log, commented-out code, dead or unused function, added TODO, import reshuffle, re-indent or formatting-only line, hunk made only of identical lines removed and re-added, or edit unrelated to the fix. Unchanged lines moved inside a hunk that changes behavior (a reorder fix) are not debris.
13. **Repo asks** (feeds repo-standards-met): put the asks recorded in step 2 next to the description and the diff. Record each required section that is missing or holds placeholder text, each required artifact absent from the diff, and, if the policy says AI use must be disclosed, whether the description names the tool and the extent in the author's own words.
14. Eval mode: every fact comes from the bundle text only; quote lines, never fetch. Live mode: plan from `plan.md` (including `## Deviations`), diff from `git diff main...HEAD` and commits from `git log main..HEAD --oneline` in the working copy, title and description from `pr_draft.md`, evidence from `test_evidence.md`, issue and thread from `gh issue view <N> --comments`, template and contributing guide from the scoped repo.

## Check execution

15. Execute the checks in this fixed order: plan-fidelity, description-honest, decisive-test-evidence, clean-diff, repo-standards-met, then the preferred check commit-hygiene.
16. For each check, read only its row in `rubric.md` and the facts recorded for it in steps 9 to 13. If a recorded fact is missing, return to the one package part the row names, record it, and continue; do not re-read the whole package.
17. Grade `pass` when the records meet the pass condition as written. Grade `fail` when a record matches a failing shape the row names. Grade `unclear` only when the evidence the row names is absent from the package (no test-evidence section at all, no description), not when it is present but weak; present-but-weak is `pass` or `fail` by the row's words.
18. A disclosed shortfall is not a fail: a work item recorded as "deferred with a reason" in step 9 passes plan-fidelity, and a repro the plan itself deferred is not required by decisive-test-evidence.
19. Write one line of evidence per grade: the quoted words, file, or hunk that decided it. Never add a check that is not in the table and never skip one that is. If a row's words feel wrong for a package, apply them anyway and note the tension in the summary.

## Verdict assembly

20. If all five required checks are `pass`, the verdict is `accept`. If any required check is `fail` or `unclear`, the verdict is `reject`; `unclear` counts as `fail`.
21. commit-hygiene appears in the summary and the JSON but never moves the verdict.
22. In the summary, name the deciding check: for a `reject`, the first required check in execution order (step 15) that failed, with its evidence quoted; for an `accept`, the check closest to failing and why it still passed.
23. Live mode: after the verdict, list any voice-guide rules the title or description breaks, quoting the rule. These are notes only.
24. Emit the summary, then the fenced JSON block with `item`, every check's `name`, `grade`, and one-line `evidence`, and the `verdict`, with nothing after it. If any step had nothing to work from, say so in the summary instead of inventing a step.
