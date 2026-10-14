# Evidence guide: where evidence lives in a PR package

<!--
Written by Bailey Souza (bs-gitem) for AI301 Unit 4, 2026-10-14, with Claude Code as
the drafting assistant, mapped against calib-01 and my own PR for
codepath/pathreview-ai301-fa26-s3#54.
-->

## Plan fidelity (harness category: silent-drift)

- **Where it lives, eval:** the `## Plan context` block (its "Scope", "Not in scope", "Files", and any deferral note) against the `### Diff` file headers and hunks, with the `### Description`'s claims about what changed.
- **Where it lives, live:** my `plan.md` (approach, in/out of scope, files, and `## Deviations`) against `git diff main...HEAD` from my branch, and the Summary and Changes sections of `pr_draft.md`.
- **What good looks like:** every changed file and hunk falls inside the plan's stated boundary or a deviation note, every promised work item is in the diff or deferred with a reason, and the description claims exactly what the diff does. Silent drift looks like an extra flag, setting, rename, or rewritten file the plan never names (more than the plan), or a promised item missing while the description says it is done (less than the plan).

## Test evidence (harness category: not-tested)

- **Where it lives, eval:** the `### Test evidence` section against the plan context's "Test plan" and repro-evidence lines; the description's testing claims.
- **Where it lives, live:** `test_evidence.md` (the unit 2 repro re-run on `main` for the before and on my branch for the after, plus `make test-unit`, `make test-integration`, `make lint`, `make typecheck` output) and the Testing section of `pr_draft.md`.
- **What good looks like:** the plan's own repro command is re-run on the changed path with its output shown, the after matches the plan's expected outcome, every failure mode the test plan names is covered, and the repo's checks have a stated result (a failing check reported with its reason still counts). "Tests pass" or "works on my machine" with no output, or a run of only the control path, is not evidence.

## Diff quality (harness category: unreviewable)

- **Where it lives, eval:** the `### Diff` and `### Commits` sections.
- **Where it lives, live:** `git diff main...HEAD` and `git log main..HEAD --oneline`; `git diff main...HEAD --stat` should list only the files the fix needs (for #54: `ingestion/parsers/resume_parser.py` and `tests/unit/test_resume_parser.py`).
- **What good looks like:** every `+`/`-` line is the fix or its test. Debris tells: debug prints (live or commented), commented-out attempts, dead helpers, added TODOs, import reshuffles, re-indent churn, lines deleted and re-added unchanged, unrelated edits.

## Standards and comms (harness category: standards-wall)

- **Where it lives, eval:** the `## Repo facts` block's "pull requests" (template sections, checklist, required artifacts like changelog or whatsnew) and "contribution policy" (AI policy wording) lines, against the description and diff.
- **Where it lives, live:** Path Review's `.github/PULL_REQUEST_TEMPLATE.md` (Summary, Issue with `Closes #`, Changes, Testing checklist, Screenshots / Demo, Notes for Reviewers), `docs/CONTRIBUTING.md`, and the house rules in `scope.md`; the course requires an own-words AI-use disclosure, which goes in Notes for Reviewers.
- **What good looks like:** every required section holds real content, the issue is linked, required artifacts are in the diff, and where the policy asks for AI disclosure the description names the tool and what it did. Boilerplate left as the template's placeholder, a missing checklist, or no disclosure under a "must disclose" policy is the wall.
