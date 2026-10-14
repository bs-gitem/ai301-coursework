# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/226

**Branch**

`fix/54-resume-section-whitespace`

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run 1: I got 17/20 agreed. Every category had at least one match (clear-accept 4/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2, unreviewable 3/3). The three I missed were all good PRs that I rejected: pkg-02 (clean-diff), pkg-05 and pkg-19 (decisive-test-evidence).
2. Partial run with --only pkg-02,pkg-05,pkg-19 plus canaries pkg-07, pkg-14, pkg-18 and calib-04: I got 6/6 scored agreed. calib-04 was still rejected.
3. Full run 2 (the one in eval-run.txt): I got 20/20 agreed, with every category matching.

**Package analysis**

pkg-19 (mikefarah/yq#2819). Gold label: accept.
In full run 1, my rubric rejected it. In full run 2, it accepted it.
The problem was my decisive-test-evidence check. At first, I wanted pasted output for everything listed in the test plan. pkg-19 had real before and after output for the issue's repro. But the two controls only said "unchanged", and go test only showed a count. So my check rejected it.
Looking back, that was too strict. The PR already showed real output proving the fix worked. The controls also had their results stated.
I changed the check so the main repro needs pasted output. Everything else named in the plan needs at least a stated result. After that change, my rubric accepted pkg-19, which matches gold.

**Check rationale**

"The evidence re-runs the plan's main repro (the issue's failing input or command, on the path the fix changes) and shows its observed after output matching the plan's expected outcome, with a before (or a reference to the reproduced before) for contrast. Every other failure mode or repro the test plan names must at least have its result stated (a second failure mode left unmentioned fails)."

I worded it this way because my first version required every repro or failure mode to be "shown". That ended up rejecting two clear accepts (pkg-05, pkg-19). Both had proof that the main fix worked, but only summarized the control results.
I also did not want to make the check too loose. calib-04 is supposed to be rejected because its plan had two failure modes. It only tested the first one and never mentioned the second.
So I made the difference clear. The main repro needs real output. Anything else named in the plan needs at least a stated result. If something is not mentioned at all, it still fails.

**Trade-offs**

I knew loosening decisive-test-evidence could cause not-tested packages to be accepted when they should not be.
To check that, I re-ran pkg-07 and pkg-14 (not-tested), along with calib-04, using --only. All three stayed reject.
I also adjusted clean-diff so unchanged lines moved as part of a reorder fix would not count as debris. That fixed pkg-02. I then re-ran pkg-18 (unreviewable) as another canary, and it stayed reject.
There is still one trade-off I am accepting. A PR could say "controls unchanged" without actually running those controls. My rubric would now accept that part because a stated result is enough for anything beyond the main repro.
That is a limitation, but I chose to keep the main repro requirement strict without rejecting good PRs just because they summarized the other results.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
