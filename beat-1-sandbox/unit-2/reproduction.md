# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

bs-gitem

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54#issuecomment-5853406003

Claiming `#54`. The symptom I’m reproducing: when parsed PDF text keeps leading indentation, `_detect_sections()` in `ingestion/parsers/resume_parser.py` misses the headings because the patterns are anchored to the start of the line. That leaves `metadata['detected_sections']` as `[]` when `Education` and `Skills` should be detected.

Next step: I’ll reproduce it from a fresh clone of the current `main` on Windows 11 with Python 3.13, using the issue’s example unchanged. Then I’ll do a control run with the same text but the leading spaces removed to confirm the indentation is what triggers it. I’ll post my environment, exact commands, and output here, then run `tests/unit/test_resume_parser.py` and show which of the three named tests fail. I’m not committing to a fix or a date in this comment; the reproduction comes first.

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54#issuecomment-5918220403

The issue's unchanged input and the control produced this output on a fresh clone of `main`:

```text
Python 3.13.14
OS Windows-11-10.0.22631-SP0
INDENTED []
CONTROL ['Education', 'Skills']
```

This reproduces #54's missing section headings when the text has leading spaces. Removing only those spaces makes Education and Skills detectable.

Environment: Windows 11 build 22631, PowerShell, Python 3.13.14, PathReview commit `2f4e82f52efbcfcc57d65b3fa5348672163ca088`, freshly cloned on September 30, 2026. No database, Docker services, or credentials were used.

Setup commands (uv was already installed):

```powershell
git -c http.sslBackend=openssl clone https://github.com/codepath/pathreview-ai301-fa26-s3.git source
uv venv --python 3.13 .venv
uv pip install --python .venv/Scripts/python.exe -e ./source pytest
cd source
```

Save this as `repro54.py`, then run `../.venv/Scripts/python.exe repro54.py`:

```python
import platform
from ingestion.parsers.resume_parser import ResumeParser
print('Python', platform.python_version())
print('OS', platform.platform())
r = ResumeParser()
text = '\n    John Smith\n    john@example.com\n\n    Education:\n    - B.S. Computer Science\n\n    Skills: Python\n'
print('INDENTED', r.parse(text).metadata['detected_sections'])
control = '\nJohn Smith\njohn@example.com\n\nEducation:\n- B.S. Computer Science\n\nSkills: Python\n'
print('CONTROL', r.parse(control).metadata['detected_sections'])
```

For the three tests named in the issue:

```powershell
../.venv/Scripts/python.exe -m pytest tests/unit/test_resume_parser.py -k 'test_parse_single_column_resume_text or test_parse_resume_no_work_experience or test_detect_sections' -q
```

```text
7 deselected, 3 xfailed in 0.23s
```

They have expected-failure markers. Running the same command with `--runxfail` exposes the assertions:

```text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_single_column_resume_text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_resume_no_work_experience
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_detect_sections
3 failed, 7 deselected, 2 warnings in 0.22s
```

The assertions fail at lines 35, 61, and 152 because the expected sections are missing. No parser code or tests were changed.

The --runxfail output above is from my own PowerShell verification. Pytest also reported two cache-write permission warnings; all three tests executed and reached their missing-section assertions. The initial expected-failure run (3 xfailed) was executed by Codex during setup.

AI disclosure: Claude Code assisted with the earlier claim and rubric.



## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Smoke run, 3 packages (`--limit 3`): 2/3 agreed. pkg-03 (ripgrep) was rejected on my `conventions-met` check.
2. `--only pkg-03,pkg-07,pkg-20` after rewording the disclosure rule: 3/3.
3. Full run 1: 18/20, bar PASS (clear-accept 6/8, disclosure 1/1, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4). Disagreements: pkg-09 and pkg-10, both honest cannot-reproduce packages that my `behavior-matches-issue` check failed.
4. `--only pkg-09,pkg-10,pkg-02,pkg-08,pkg-17` after rewriting that check (the three wrong-target packages were canaries for the loosened condition): 4/5 — pkg-10 flipped to agree, pkg-09 still rejected, all three canaries held.
5. `--only pkg-09` re-run with no further change: 1/1 (accept). The earlier miss was grader variance on the same rubric text.
6. Full run 2 (confirming, `--save-run eval-run.txt`): 20/20, bar PASS, every category 100% (clear-accept 8/8, disclosure 1/1, no-evidence 4/4, unfollowable-comms 3/3, wrong-target 4/4). This matches the committed `eval-run.txt`: `agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

pkg-09 (sharkdp/fd #2033, `--exec-batch` ordering). Gold label: accept. My rubric in full run 1: reject, on `behavior-matches-issue` and `control-run`. The report is an honest cannot-reproduce: the author ran the issue's exact `--exec-batch` scenario with markers appended to a log, five times over an ARG_MAX-sized argument list, and every run wrote all ONE batches before any TWO batch. My check, as first written, asked whether "the artifact shows the same behavior the issue describes", and a cannot-reproduce artifact by definition does not show the bug, so the grader read a faithful negative result as a wrong-target failure. The gold label reads it the other way: the trigger was the issue's own, the environment was recorded, and the author named what differed (uniform name lengths, a 2 MiB ARG_MAX) and what a maintainer could try next. My first check could not see that, because it judged the outcome instead of the fidelity of the attempt.

**Check rationale**

The check exactly as it reads now in the `rubric.md` I uploaded to `tools/repro-check/`:

> | behavior-matches-issue | The artifact read against the issue's described behavior (the exact error, exit code, crash, missing header, visible symptom), AND the input/command used read against the issue's trigger (its syntax, flag, file, or sequence). | First read the report's own conclusion. If it claims the bug reproduced: the artifact shows the same behavior the issue describes, produced by the same trigger the issue describes; a different error (a graceful validation error where the issue reports a crash; a compile error where the issue reports a runtime error), a different symptom, or an input that is not the issue's trigger fails, no matter how confident the narration. If it claims it could NOT reproduce: this check passes when the artifact shows the issue's own trigger being run (same command, input, or sequence) and the actual outcome, even though that outcome is the normal one; the honesty of that conclusion is judged by honest-outcome, not here. An artifact from a trigger that is not the issue's fails in both cases. | required |

Why it reads that way: the first version had only the "if it claims the bug reproduced" half. I rejected the alternative of simply exempting cannot-reproduce reports from the check, because that would let a report run the wrong command, see nothing, and call it a cannot-reproduce; the trigger-fidelity half has to apply to both outcomes. Splitting the condition on the report's own conclusion keeps the wrong-target trap (calib-03's operator swap, pkg-02's prefix range instead of the offset syntax) while letting an honest negative through.

**Trade-offs**

Loosening a required check can flip a package that previously agreed, so I re-ran the change with three canaries from the wrong-target category (pkg-02, pkg-08, pkg-17) in the same `--only` run as the two packages I was trying to fix. All three stayed at reject, which is how I know the loosened condition did not open the door to adjacent-symptom reports. What the check still gives up: a cannot-reproduce whose command matches the issue but whose environment silently differs from the issue's target (pkg-16's shape) is caught by `honest-outcome`, not by this check, so if that check were ever removed the trap would be gone.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.

