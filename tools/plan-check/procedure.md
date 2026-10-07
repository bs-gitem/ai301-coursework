# Procedure: how this skill grades a plan package

<!--
Written by Bailey Souza (bs-gitem) for AI301 Unit 3, 2026-10-07, with Claude Code as
the drafting assistant. These are the operating steps for GRADING a plan package, not
for writing one. A groupmate who has never seen a package should be able to follow them
and land on the same grades I would. Where the steps say "record", write the fact down
before moving on; the grades in Check execution are built only from what was recorded.
-->

## Read order

1. Eval mode: read the package header (`source:`, `captured:`, `calibration:`). Live mode: read `scope.md` first and stop if the issue is outside the scoped repo or the `Repo:` line is still a placeholder.
2. Read the **Repo facts** block next. Record three facts verbatim: the bug-report template asks, the contribution-policy line, and the exact AI-policy wording (quote it; the words "must disclose" or "all AI usage must be disclosed" decide ai-disclosure later).
3. Read the **Issue** title and body. Record in one sentence: the trigger (the input, command, flag, or sequence) and the symptom (the error, empty value, crash, color, or measurement the reporter saw). This is the "issue's symptom" every later check compares against.
4. Read the **Thread highlights**. Record, per comment: who wrote it, their role tag (OWNER, MEMBER, CONTRIBUTOR, COLLABORATOR count as maintainer-side; NONE does not), and whether it gives explicit direction: an isolated culprit or file, a proposed approach, a rejected approach, a works-as-intended note, a request for testing, or a prior-art PR. If no comment gives direction, record "no maintainer direction".
5. Read the **Repro evidence** block BEFORE the plan. Record: the environment line, each numbered step, each artifact (quote the decisive line), every control run and what it holds constant versus changes, and the Expected and Actual lines. Write down in one sentence what the controls rule OUT. Reading the repro before the plan matters because a confident plan can talk you into its cause; the controls must be fixed in mind first (this is the calib-03 trap).
6. Only now read the **Candidate plan**. Record: the stated cause sentence; the in-scope and out-of-scope statements; every file, module, or function named; the approach steps in order; the test plan; the risks or unknowns lines; any deviation note.
7. Read the **Candidate plan comment** last. Record: which thread direction it mentions (if any), whether it names a tool for AI use, what it promises, and whether it departs from the plan body.
8. Live mode only: after step 7, read `voice-guide.md` and hold the draft comment against each rule; note broken rules for the summary. This never changes a grade.

## Evidence gathering

9. **Diagnosis and grounding** (feeds cause-grounded): put the plan's cause sentence next to the recorded controls. Ask: does any control run show the blamed component working? Does any step produce an artifact the cause cannot explain? Does the plan wave away a step or artifact ("red herring", "side effect", "not the real issue") without showing a run that supports the wave? Record the answer with the step number or artifact line that decided it.
10. **Scope** (feeds one-bounded-change): list every planned work item from the approach steps and the in-scope line. Mark each item either "fixes or verifies the issue's symptom" or "other". Record the "other" items verbatim; record any item the plan explicitly defers with a reason as "deferred, with reason".
11. **Executability** (feeds stranger-can-start): record every file, module, function, or document named for change. Record every sentence that leaves a choice open, quoting the words ("investigate", "whichever", "or", "maybe", "not sure", "somewhere"). Record whether a default is chosen when an alternative is named.
12. **Test plan** (feeds test-observable): record the outcome the test plan says will be observed after the fix, in the plan's own words, and which repro step or artifact it maps onto. If the test plan only names a process (run the suite, profile, check it feels right) and no outcome, record "no outcome named".
13. **Honesty** (feeds risks-named): record each unknown or risk the plan names as an unknown. Record any claim of certainty about something no step or artifact in the package shows.
14. **Comms** (feeds thread-engaged, ai-disclosure, conventions-followed): put the recorded thread direction (step 4) next to the plan and the comment and record whether the direction is followed, acknowledged with a reason for departing, or absent from both. Put the recorded AI-policy wording (step 2) next to the comment and record whether the comment names an AI tool and the extent of its use. Put the stated contribution asks next to the plan and record any contradiction.
15. Eval mode: every fact above comes from the bundle text only; quote lines, never fetch. Live mode: issue, thread, and repo facts come from GitHub (`gh issue view --comments`, the repo's CONTRIBUTING, PR and issue templates, any AI policy file); repro evidence comes from the student's own posted repro comment on the issue, or from the house repro pack as quoted in the drafts; the plan and comment come from the drafts and nothing else in the working directory.

## Check execution

16. Execute the checks in this fixed order: cause-grounded, one-bounded-change, stranger-can-start, test-observable, thread-engaged, ai-disclosure, then the preferred checks risks-named and conventions-followed. Required checks first, in the order the lecture's failure families run from "wrong" to "unwelcome", so the deciding fail is found early and quoted precisely.
17. For each check, read only its row in `rubric.md` and the facts recorded for its evidence family in steps 9 to 14. Do not re-read the whole package for a check; if a recorded fact is missing, go back to the one block the rubric row names, record the fact, and return.
18. Grade `pass` when the recorded facts meet the pass condition as written. Grade `fail` when a recorded fact matches a failing shape the row names. Grade `unclear` only when the evidence the row names is genuinely absent from the package (for example, the plan has no test plan at all, or the thread-highlights block is missing), not when it is present but weak; present-but-weak is `pass` or `fail` by the row's words.
19. For every grade, write one line of evidence: the quoted words or the step or artifact number that decided it. "Looks fine" and "seems thorough" are not evidence; a grade with no quotable fact is not finished.
20. Apply the row's words even when they feel wrong for this package. If a package passes every row and still looks unready, note the tension in the summary; the fix belongs in `rubric.md`, not in this run. Never add a check that is not in the table, and never skip one that is.
21. When two rows seem to fail on the same fact (a docs-only workaround can look like both a scope choice and an ignored thread), grade each row on its own words. A fact may decide two rows.

## Verdict assembly

22. Collect the six required grades. If all six are `pass`, the verdict is `accept`. If any required grade is `fail` or `unclear`, the verdict is `reject`; `unclear` enters the rule as `fail`, exactly as the rubric's verdict rule says.
23. The preferred grades (risks-named, conventions-followed) go into the summary and the JSON checks list but never move the verdict in either direction.
24. In the summary, name the deciding check for a `reject` (the first required check in execution order that failed) and quote the evidence line that decided it. For an `accept`, name the check that was closest to failing and quote why it still passed.
25. Live mode: after the verdict, list any voice-guide rules the draft comment breaks, quoting the rule. Report them as notes; they do not change the verdict.
26. Emit the summary, then the fenced JSON block with `item`, every check's `name`, `grade`, and one-line `evidence`, and the `verdict`, and nothing after the JSON block. If any step above had no information to work from (a procedure gap), say so in the summary rather than inventing a step.
