# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

bs-gitem

**Plan comment**

PENDING-LINK

Plan for #54. This is based on my repro on commit `2f4e82f`, Windows 11 and Python 3.13.

**Cause**

The issue is in `_detect_sections()` in `ingestion/parsers/resume_parser.py`. The four regex patterns on lines 134-137 expect the section name right after `^` or `\n`. If there are spaces before a header, it doesn't match it.

My repro made this pretty clear. The issue input gave me:

`INDENTED []`

I ran the same text again with only the leading spaces removed and got:

`CONTROL ['Education', 'Skills']`

That was the only difference between the two runs. So the section names themselves are fine. The problem is the leading whitespace.

**Change**

I'm going to add `[ \t]*` after the `^` and `\n` anchors in the four patterns. That lets it accept spaces or a tab before the section name without letting the regex jump across another line.

I'm also removing the strict `xfail` markers from these three tests:

- `test_parse_single_column_resume_text`
- `test_parse_resume_no_work_experience`
- `test_detect_sections`

Once the fix works those should just pass normally. If I leave the strict markers there, CI would fail on `XPASS`.

Branch: `fix/54-resume-section-whitespace`

**Not changing**

I'm leaving `_strip_markdown()` and the two markdown tests alone. They also have a `#54` marker, but its a different problem. Those fail because `^#+\s+` doesn't strip an indented `# Header`. That's happening in another function.

I don't want to mix another fix into this one just because it has the same issue marker. I saw the plans above from Pometnova, arulagarwal, jjinacio and fukubie that include both. If the maintainer wants both in the PR I can add it as a second commit.

**Test**

Rerun `repro54.py`. Expected: `INDENTED ['Education', 'Skills']`

Then run the three tests from the issue. Expected: `3 passed`

Then run all of `tests/unit/test_resume_parser.py`. Expected: `8 passed, 2 xfailed`, no `XPASS`.

After that I'll run `ruff`, `black --check` and `mypy` on the two files I changed.

**Unknowns**

I haven't tested a real PDF that comes out tab-indented. `[ \t]*` covers tabs in the regex, but I don't have an actual sample proving that yet.

`detected_sections` comes from a `set`, so the order isn't stable either. The tests don't depend on the order and its unrelated to this bug so I'm not changing it.

I also have six `test_review_service.py` tests failing on untouched `main` because of the database setup in my environment. Those were already failing and aren't part of this change.

Drafted and checked with Claude Code. The repro and test runs are mine.

---

## Your branch

**Branch**

fix/54-resume-section-whitespace

**Evidence**

Before (my Unit 2 repro, posted on the issue 2026-09-30, commit `2f4e82f`):

```powershell
../.venv/Scripts/python.exe repro54.py
```

```text
Python 3.13.14
OS Windows-11-10.0.22631-SP0
INDENTED []
CONTROL ['Education', 'Skills']
```

```powershell
../.venv/Scripts/python.exe -m pytest tests/unit/test_resume_parser.py -k 'test_parse_single_column_resume_text or test_parse_resume_no_work_experience or test_detect_sections' -q
```

```text
7 deselected, 3 xfailed in 0.23s
```

Same command with `--runxfail`:

```text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_single_column_resume_text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_resume_no_work_experience
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_detect_sections
3 failed, 7 deselected, 2 warnings in 0.22s
```

Before, re-run on 2026-10-07 on the fork at `2f4e82f` right before the change (fresh `uv venv --python 3.13`, `uv pip install -e . pytest`):

```text
=== BEFORE: repro54.py
Python 3.13.5
OS Windows-11-10.0.22631-SP0
INDENTED []
CONTROL ['Education', 'Skills']
=== BEFORE: 3 tests
7 deselected, 3 xfailed in 0.44s
=== BEFORE: --runxfail
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_single_column_resume_text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_resume_no_work_experience
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_detect_sections
3 failed, 7 deselected in 0.43s
```

After (branch `fix/54-resume-section-whitespace`, commit `a29ac3b`, same venv, same script, 2026-10-07):

```text
=== git diff --stat
 ingestion/parsers/resume_parser.py | 13 ++++++++-----
 tests/unit/test_resume_parser.py   |  9 ---------
 2 files changed, 8 insertions(+), 14 deletions(-)

=== AFTER: python repro54.py
Python 3.13.5
OS Windows-11-10.0.22631-SP0
INDENTED ['Education', 'Skills']
CONTROL ['Education', 'Skills']

=== AFTER: the three tests named in #54 (markers removed)
...                                                                      [100%]
3 passed, 7 deselected in 0.31s

=== AFTER: whole tests/unit/test_resume_parser.py (expect 8 passed, 2 xfailed, no XPASS)
...x...x..                                                               [100%]
=========================== short test summary info ===========================
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_parse_markdown_resume - issue #54: resume section detection fails on leading whitespace
XFAIL tests/unit/test_resume_parser.py::TestResumeParser::test_strip_markdown_syntax - issue #54: resume section detection fails on leading whitespace
8 passed, 2 xfailed in 0.39s

=== AFTER: full unit suite
FAILED tests/unit/test_review_service.py::TestReviewService::test_create_review_returns_review_with_pending_status
FAILED tests/unit/test_review_service.py::TestReviewService::test_create_review_calls_db_add
FAILED tests/unit/test_review_service.py::TestReviewService::test_create_review_calls_db_commit
FAILED tests/unit/test_review_service.py::TestReviewService::test_create_review_calls_db_refresh
FAILED tests/unit/test_review_service.py::TestReviewService::test_create_review_with_uuid_ids
FAILED tests/unit/test_review_service.py::TestReviewService::test_review_sections_and_score_initially_none
6 failed, 372 passed, 50 xfailed, 20 warnings in 7.54s
```

The same six `test_review_service.py` tests fail on untouched `main` in this environment (`6 failed, 369 passed, 53 xfailed`); they need a database the unit run does not have here and are not part of this change.

```text
=== lint/format/typecheck on the two changed files
ruff check: All checks passed!
black --check: 2 files would be left unchanged.
mypy: Success: no issues found in 1 source file
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

I started with a smaller smoke run using `--only` with `--include-calibration`. That covered 7 packages: `calib-01` through `calib-04`, plus `pkg-04`, `pkg-09` and `pkg-20`. The 3 scored items all agreed and all 4 calibration packages agreed too. That run doesn't count toward the actual bar though, so I did a full run after.

Full run 1: `19/20, PASS`

Breakdown:

- clear-accept: 6/7
- scope-creep: 4/4
- thread-convention: 2/2
- unbuildable: 3/3
- wrong-cause: 4/4

That's the run saved in `eval-run.txt`. I didn't change the rubric after it, so I didn't do another full run.

**Package analysis**

The only disagreement was `pkg-14`, `zellij-org/zellij#5174`. My rubric says reject. The gold label says accept.

Pretty much everything else in that package was good. The cause matched the repro and the `0.44.1` control. The scope was bounded. The Windows version was deferred for an actual stated reason. The test plan also gave a real target, five SSH reattach cycles with no RGB strings showing up in any pane. There wasn't any maintainer direction in the thread that changed the plan either.

What failed it for me was `stranger-can-start`. The plan names the general areas across two crates, but then says the exact functions will be figured out in the PR after tracing the query with debug logs. I read that as an actual implementation decision still being left for build time. That's the same kind of thing the check is supposed to catch in packages like `pkg-17` and `pkg-18`.

I can see why the gold answer accepted it though. The author already has the debug trace working and seems to know where the problem starts. So you can make the argument that its ready enough even without the exact function being named yet.

I decided not to change my check around that one case. If I loosen it to say naming the general area is enough and the exact function can come later, then I start opening the door for stuff like `pkg-18` too. I'd rather have one close miss like this than make the check loose enough that plans you really can't start building begin to pass.

**Check rationale**

The check is `stranger-can-start`. Here is its pass condition, exactly as it reads in the `rubric.md` I uploaded to `tools/plan-check/`:

> The plan names at least one concrete file, module, function, or document to change AND states one chosen approach for it. It fails if a real decision is deferred to build time: "investigate", "profile and optimize", "poke around", "somewhere", "whichever is easier", "not sure which layer", "maybe also check", or two approaches offered with no choice made. An open question passes only when the plan picks a default and names the alternative as a fallback under a stated condition. Terse passes; a single named file with a single stated change is enough.

The basic idea is that another person should be able to pick up the plan and actually start it. It doesn't need to be long. It doesn't need five sections and a huge explanation either. It needs at least one real place to change, like a file, module, function or document. It also needs an approach that is already chosen.

What I don't want is the important part of the work still being "investigate", "profile and optimize", "poke around", "somewhere", "whichever is easier", "not sure which layer" or "maybe also check". Those are all basically saying the person doing the implementation still has to make the plan first.

An open question is different if the author already picked a default. For example, if the plan says use approach A first, and only switch to B if a specific benchmark shows X, thats still actionable. There is a starting point and a reason to change it.

`calib-01` is why I don't grade this based on how detailed the plan looks. That one is thin but still ready. It names the place and says what to change. That's enough.

`pkg-20` is why I allow a real open question when there's already a default choice. It raises the placement question, but chooses per-use first and tells you what benchmark result would make it move. That's very different from leaving two options there and basically saying figure it out later.

I also pulled the failure language from the unbuildable packages themselves, mainly `pkg-10`, `pkg-17` and `pkg-18`. I wanted the check to be based on patterns another grader can actually recognize. Not just "this plan feels vague."

**Trade-offs**

The trade-off is basically `pkg-14`. It's a good plan in a lot of ways. The author has evidence and knows the area of the code, but leaves the exact function for the PR. My rubric rejects it. The gold label accepts it. I'm okay with that being the miss for now. Changing the rule enough to let `pkg-14` through could also make it easier for plans that really are unfinished to get through.

I also didn't rerun individual canaries after the full run because I didn't change anything after it. I checked that with the hashes. `eval-run.txt` recorded `rubric.md sha256:4017c4370f2ace6d`. The `rubric.md` I uploaded has `4017c4370f2ace6d`. Same hash. So the rubric from the full run is the same one that got uploaded.

The case I know this check can still miss is somebody who actually has the evidence and understands the problem, but intentionally doesn't name the exact function until they're inside the implementation. I'm accepting that trade-off for now because I think keeping `stranger-can-start` concrete is more useful than trying to make it handle every borderline case.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
