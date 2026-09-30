# Evidence guide: where proof lives in a reproduction package

<!--
Written by Bailey Souza (bs-gitem), AI301 Unit 2, 2026-09-26. Each family says where to look
and what good looks like, in conditions someone else could apply.
-->

## Environment

- Where it lives: eval bundle → the repro report's first lines or a labelled "Environment" / "Setup" block (OS, version, commit, runtime); the issue context's opening lines and the repo-facts "bug reports: template asks for…" line say which fields the project itself considers necessary. Live → the student's draft repro comment, plus the repo's issue template (`.github/ISSUE_TEMPLATE/`) and the issue's own body for the target version and platform.
- What good looks like: the OS and the project version or commit that actually ran are stated, and they match what the issue targets, or the difference is named in the report. If the issue is platform-specific (Windows-only, a particular driver or shell), that platform detail is present. "Latest version" without a number, or no record at all, is not an environment.

## Steps

- Where it lives: eval bundle → the numbered or listed steps in the repro report, from the starting state (fresh clone, install command, config file contents) to the action that triggers the behavior. Live → the same in the draft, read next to the issue's own "steps to reproduce".
- What good looks like: each step is a literal command, input, or click that a stranger could perform on the recorded environment, and the last step is the issue's trigger itself (the same syntax, flag, file, or sequence the issue names). Steps that depend on a private repository, an unshared config, or "my project" cannot be followed and fail. Terse is fine; a two-line repro with the exact command is followable.

## Behavior shown

- Where it lives: eval bundle → the artifacts inside the repro report: fenced output, log excerpts, error text, screenshots described, measurements, exit codes. Live → the same in the draft, plus the issue thread's own artifacts for comparison.
- What good looks like: the artifact was produced by the author's own run (it appears verbatim, not summarised), and it shows the behavior the issue describes: the same error message, exit code, crash, missing header, or visible symptom. An artifact that shows only that the program runs, a graceful validation error in place of the reported crash, a compile error in place of a runtime error, or output from a different input than the issue's trigger does not show the issue's behavior, however confidently it is narrated. A control run (the non-failing case) next to the failing run is the strongest form.

## Honesty

- Where it lives: eval bundle → the report's conclusion sentence(s) read against its own artifacts and environment record, and against the issue's stated target; the claim comment's promises read against what the report later delivers. Live → the draft's conclusion against the artifacts in the same draft.
- What good looks like: the conclusion says exactly what the artifacts show and nothing more. "Reproduced" is backed by an artifact showing the issue's behavior. "Could not reproduce" is backed by a real attempt, a recorded environment, and a sentence naming what differed from the issue (version, platform, config) and what a maintainer could try next; that is a pass. Red flags that fail: certainty words ("guaranteed", "obviously", "definitely the null-result thing") with no artifact; a root cause asserted without evidence; reproducing on an older version than the issue targets without saying so; generalising the bug beyond what was shown.

## Comms

- Where it lives: eval bundle → the candidate claim comment read against the issue title and body; the repo-facts block's "bug reports: template asks for…", "contribution policy", and any stated AI policy line, read against both comments. Live → the draft comments, the repo's CONTRIBUTING.md and issue template, and the Path Review house rules in scope.md.
- What good looks like: the claim names this issue's specific symptom or trigger in the author's words and promises only the next step (reproduce, investigate), never a fix, a date, or a guarantee; it could not be pasted unchanged on another issue. Where the repo's stated policy requires disclosing AI assistance, both comments disclose it plainly (course packages are AI-assisted by definition). Where the template asks for a field (logs, OS, version), the report supplies it. Boilerplate ("+1", "same here", "please assign me, I'll have a PR in 2 days") fails; a plain, specific, modest sentence passes.
