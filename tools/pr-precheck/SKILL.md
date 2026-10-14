---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: **is this ready to submit?** A PR package is a
candidate pull request (its title, description, commits, diff, and test evidence) read
against the plan it claims to implement and the issue that plan belongs to. Do not
review code style, propose fixes, or grade a second package in the same run. Do not
answer from gut feel: execute `procedure.md`, which applies `rubric.md`.

## Inputs and modes

- **Live mode** (the student's own PR before it opens). Read, from the student's working
  copy of their fork, on their branch:
  - `plan.md`, including its `## Deviations` section;
  - the branch's diff: run `git diff main...HEAD` (three dots) and `git log main..HEAD --oneline`
    in the working copy; only committed changes count;
  - `pr_draft.md`: the PR title on the first line, then the filled PR template;
  - `test_evidence.md`: the repro before/after and the repo's check output;
  - the issue and its thread: `gh issue view <N> --repo <scoped repo> --comments` for the
    issue URL the student gives;
  - the scoped repo's `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`.
  A house-chain student reads the house plan and house repro pack in place of their own
  plan and repro; the same checks grade them. The package is what these files contain;
  do not grade other files in the directory.
- **Eval mode** (a package bundle: repo facts, issue, thread highlights, plan context,
  and the candidate PR's title, description, commits, diff, and test evidence). The
  bundle is the whole world: use only its text, fetch nothing, run nothing. Always grade
  the complete package: every check, full verdict rule.

## The scope seam (live mode only)

Read `scope.md` before anything else in live mode. Apply its house rules when reading
evidence (PR from the student's fork branch, one PR per issue, template always used).
Refuse to grade a PR whose issue or target repo is outside the scoped repo. If the
`Repo:` line still holds a placeholder such as `<ORG>/<PATH-REVIEW-REPO>`, stop without
grading and tell the student to fill the `Repo:` line in `scope.md` with their
section's Path Review repo; never guess a scope. In eval mode, ignore `scope.md`.

## The voice seam (live mode only)

In live mode, read `voice-guide.md` after the package and hold the outgoing text, the PR
title and description in `pr_draft.md`, against each rule. Report each broken rule in
the summary, quoting the rule and the offending words. The voice guide never changes
the verdict on its own, because no rubric check reads it; voice is personal. In eval
mode, ignore `voice-guide.md`.

## Component reads

- `rubric.md` defines the checks (name, evidence, pass condition, weight) and the
  verdict rule. Grade only its checks, all of them.
- `references/evidence-guide.md` maps where each evidence family lives, in a bundle and
  live, and what good looks like.
- `procedure.md` is executed as written: its read order, its gathering steps, its check
  order, its verdict assembly. When the procedure is silent on a step, report the gap in
  the summary; never improvise around it.
- If `rubric.md` has no checks or no verdict rule, or `procedure.md` has no steps, refuse
  to grade and say which file is empty. Never invent checks at runtime.

## Verdict and output

The verdict is binary: `accept` means submit the PR, `reject` means hold it. No third
verdict, no score; reservations go in evidence lines. Write a short per-check summary
first, then end the reply with this fenced JSON block, valid, last, and with nothing
after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: every grade names the quote, file, or hunk that decided it. "Looks
  fine" is not evidence.
- Grade the thing, not the polish: read the diff against the plan, the evidence against
  the test plan, the description against the diff. A terse complete PR can pass; a
  polished one hiding drift fails.
- The rubric decides: if a check passes by its stated condition but feels wrong, it
  passes, and you note the tension.
- The procedure decides how: follow its steps in order and report its gaps.
- Unclear counts as fail. Where the rubric's verdict rule is silent, an unverifiable
  claim is a failing one: a PR you cannot verify from the package is not ready.
