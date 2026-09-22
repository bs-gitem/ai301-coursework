# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54 — "Resume section detection fails on text with leading whitespace" (labels: bug, good first issue, ingestion, tier-1).

**Verdict output**

Live-mode run on 2026-09-22 against three candidates (#54, #61, #73), pasted verbatim. The skill ranked #54 first; verdict `accept`.

````
All three candidates come from the correct scoped repo (`codepath/pathreview-ai301-fa26-s3`), so the Path Review house rule applies: shared claim comments don't block anything here — but in fact none of the three even have comments, assignees, or linked PRs.

**Repo-level facts (shared by all three):**
- Not archived; last push 2026-09-16 (6 days before today) — well inside 90 days.
- Last 5 default-branch commits are all by a human author (`Aburke225`), no bots.
- `docs/CONTRIBUTING.md` sets workflow rules (branch naming, conventional commits, CI-green requirement, xfail-marker removal) but states no AI ban and has no dedicated AI policy file — silence passes.

## Summary

**1. Issue #54 — Resume section detection fails on text with leading whitespace** (accept)
Best fit: it's a regex/text-extraction bug in `ingestion/parsers/resume_parser.py`, squarely inside the NLP coursework you named, and it ships the clearest acceptance condition of the three — an exact repro script with expected vs. observed output, plus three named failing unit tests to turn green. Also literally the worked example in the repo's own CONTRIBUTING.md.

**2. Issue #61 — Health check DB probe passes a raw SQL string (SQLAlchemy 2.x)** (accept)
Solid, bounded Python bug fix (wrap `"SELECT 1"` in `sqlalchemy.text()`) with clear repro steps (`GET /health` or a unit test). Slightly less matched to your stated background since it hinges on ORM-specific API knowledge rather than the regex/NLP work you've done, but still a clean single-file fix.

**3. Issue #73 — README and `.env.example` disagree about the LLM API key** (accept)
Good, low-risk docs/config alignment task (maintainer even estimates 1–2 hours), but it's the thinnest on "reproduce a bug and ship a tested change" — there's no bug to reproduce or test to turn green, just two files to reconcile against `core/config.py`. Ranks last per your stated preference for Python bugs over docs/config work.

All three pass every required check (repo-alive, scope-fits, nobody-on-it, policy-allows); none has an unclear grade.

```json
[
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "archived:false, pushed_at 2026-09-16 (6 days pre-capture), last 5 commits all authored by human Aburke225"},
      {"name": "scope-fits", "grade": "pass", "evidence": "single bounded bug in _detect_sections() of resume_parser.py, with an exact repro script and 3 named failing tests to fix"},
      {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: [], 0 comments, no cross-referenced/linked PRs found via issue timeline or PR search"},
      {"name": "policy-allows", "grade": "pass", "evidence": "docs/CONTRIBUTING.md sets workflow rules but states no AI-contribution ban"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/61",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "same repo facts: not archived, pushed 2026-09-16, human commits"},
      {"name": "scope-fits", "grade": "pass", "evidence": "single bounded bug in api/routes/health.py raw-SQL-vs-text() mismatch, with a stated repro (GET /health or unit test) and error message"},
      {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: [], 0 comments, no linked/cross-referenced PRs found"},
      {"name": "policy-allows", "grade": "pass", "evidence": "same CONTRIBUTING.md, no AI ban"}
    ],
    "verdict": "accept"
  },
  {
    "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/73",
    "checks": [
      {"name": "repo-alive", "grade": "pass", "evidence": "same repo facts: not archived, pushed 2026-09-16, human commits"},
      {"name": "scope-fits", "grade": "pass", "evidence": "one concrete docs/config fix (README.md and .env.example disagree on LLM key), maintainer-estimated 1-2 hours"},
      {"name": "nobody-on-it", "grade": "pass", "evidence": "assignees: [], 0 comments, no linked/cross-referenced PRs found"},
      {"name": "policy-allows", "grade": "pass", "evidence": "same CONTRIBUTING.md, no AI ban"}
    ],
    "verdict": "accept"
  }
]
```
````

---

## Eval iterations

**Run history**

1. Run 1 — `agreement: 14/20 scored items  (bar: 18/20: below the bar)`. Categories: `claimed 4/4  clear-accept 2/8  dead-repo 3/3  policy 1/1  scope 4/4`. Every miss was a false reject: `repo-alive` failed issue-01, 09, 14, 16 and `scope-fits` failed issue-01, 04, 19.
2. Run 2 — `agreement: 18/20 scored items  (bar: 18/20: PASS)`. Categories: `claimed 4/4  clear-accept 7/8  dead-repo 3/3  policy 1/1  scope 3/4`. This is the run committed as `eval-run.txt`.

**Issue analysis**

`issue-01` (conda/conda#16475). Gold label: `accept` ("docs task with a stated home and scope; active repo, unclaimed"). My rubric: `reject`, both runs, `failed: scope-fits` (run 1 also failed `repo-alive`).

The bundle says: "The `conda install` workflow for supported PyPI packages is going GA/stable (in September), but the docs do not yet have a permanent home for it." My `scope-fits` check fails an issue that is "a one-line wish with no spec and a product decision hiding inside". The grader read "where should the permanent home be" as that hidden product decision, because the issue proposes a page (`manage-pkgs.rst`) rather than stating one. The gold label treats the proposed page as a stated home. The difference is whether a contributor-proposed location counts as settled scope. I kept my wording because the same clause is what rejects issue-20 ("one-line feature wish with no spec"), and I would rather miss one docs task than let feature wishes through. In run 1 `repo-alive` also failed it: conda's only sampled maintainer response was `32.9 days`, over my 30-day line, even though humans committed the day before capture. That was a rubric bug, fixed in the check quoted below.

**Check rationale**

Quoted from `tools/issue-select/rubric.md` as currently written:

> `repo-alive` | repo-facts block: "archived", "last push to any branch", "last 5 default-branch commits", "maintainer first-response sample" | repo is not archived AND last push is 90 days or less before the capture date AND at least 1 of the last 5 default-branch commits is by a human author (not a name ending in [bot]). Human commits alone prove life; the response sample is consulted only when all 5 commits are bots, and then at least 1 sampled issue must show a maintainer comment within 30 days. An empty or slow response sample does not fail a repo with human commits | required

Reasoning: run 1 required a maintainer comment within 30 days on every repo. That rejected conda (issue-01, 09, 16: one sampled reply at 32.9 days) and lq-ai (issue-14: an empty sample) even though humans had pushed within a day of capture. Commits by named humans are the stronger liveness signal; the response sample only matters when the commit log is all bots, which is exactly the shape of `calib-04` (sharkdp/bat: five dependabot commits, three of five sampled issues never answered). So the sample became a fallback instead of a gate.

**Trade-offs**

The loosening flipped issue-09, issue-14 and issue-16 from `reject` to `accept` in run 2 (all gold `accept`), and issue-01 stopped failing `repo-alive`. It gave up one thing: a repo whose maintainers commit but never answer issues now passes `repo-alive`. I accept that miss because such a repo will still fail `nobody-on-it` or `scope-fits` when the thread shows the neglect, and because the dead-repo category stayed `3/3` in run 2 (issue-02, 07, 17 still reject on push date and archived status). The bot-only fallback keeps `calib-04` rejecting.

---

## Selection rationale

**Selection rationale**

1. **Fit.** It is a regex and text-parsing bug in `ingestion/parsers/resume_parser.py`. My coursework so far is Python with NLP token and regex work, so this is the one candidate where I already know the tools. It is tier-1, single file, and comes with an exact repro script and three named failing tests, so I can prove I fixed it in the evenings I have this week without needing to learn a new framework first.
2. **What the verdict got right, and what I weighed that it could not.** The skill was right that the repo is alive (human commits six days before the run), that nobody is on it (no assignee, no comments, no linked PR) and that the scope is one function. What the rubric cannot see is that #54's fix is the worked example in the project's own CONTRIBUTING.md, and that #61 needs SQLAlchemy 2.x knowledge I do not have while #73 has no test to turn green. Those tipped me to #54 over the other two accepts.
3. **Anticipated difficulty in claiming it.** Low. Zero comments and no assignee today, but it is a "good first issue" in a classroom repo, so other students in the section are likely reading the same list. The risk is a race, not a maintainer saying no. I will claim it with a comment the same day Unit 2 opens and check the issue for new comments right before I do.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
