# Contribution: README says ModelOutputError for unparseable tool calls (agentling #21)

**Repository:** [lunarstack-co/agentling](https://github.com/lunarstack-co/agentling)  
**Issue:** [#21](https://github.com/lunarstack-co/agentling/issues/21)  
**Pull Request:** [#37](https://github.com/lunarstack-co/agentling/pull/37)  
**Status:** Merged  
**Fix commit:** `9758d5e` on `docs/readme-model-output-error`

## Toolkit

**Unit 5: My issue scout and search queries**

I built my issue scout using four `gh` searches from my fit profile and a rubric to grade what came back. My first scan on October 25 could not finish because a non-interactive run needed permission for the ranking script, and another attempt failed before it started. The interactive run worked and found 345 results across 80 repos, with 4 accepts and 1 reject from the top 5, and the reject was right because the owner's own PR already covered it. The problem I found was that ranking by fewest comments kept putting brand-new issues from tiny repos near the top, so I wrote a search patch but have not applied it.

**Unit 6: Voice guard and repo memory**

I installed the voice guard using my own rules, but on October 28 it could not find `claude` on my PATH, so the hook now adds the desktop app's `claude.exe` itself. On November 3 it stopped my #21 claim twice, including the rules "I promise what I will do next (reproduce, investigate, report), never a fix, a date, or a guarantee." and "A claim says which issue, the first thing I will do, and when I will report back, measured in my real evening and weekend blocks." before passing the third version. On November 4 it also stopped my PR's AI disclosure for breaking "One short sentence, no apology." and I shortened it. I wrote my 38-line `CLAUDE.local.md` on November 8, after the PR merged, with the contributing rules, files involved, check commands, Windows test exception and "AI policy: none stated", then kept the file out of git, so it did not help during the PR.

**Unit 7: Ship-gate and my manifest**

My `ship-manifest.md` came from the repo's `CONTRIBUTING.md` and included `uv sync`, `ruff check`, `ruff format --check`, `mypy` and `pytest`. I documented why I excluded `test_parallel_timeout_keeps_other_results` on Windows, since it also failed on `main` before my change. The gate ran those 5 repo checks plus 6 PR precheck checks on November 4, everything passed, and it let `gh pr create` go through. At first it was global and could not find `claude`, so I made it opt-in per repo and added a `claude.exe` fallback, and the hook is still installed that way.

**Unit 8: PR monitor and waiting playbook**

My playbook says to wait 7 quiet days before checking in, send only one nudge in the PR, and walk away 30 days after that if nothing happens. On November 4 the monitor read PR #37 as quiet with no comments and "no checks reported", so it drafted a wait note instead of a message to post, and I posted nothing. My 7 days would have run to November 12, but folathecoder approved and merged the PR on November 5, so the nudge rule never came up. The checks were not missing, CI had been created and was waiting for the maintainer to start it, then Python 3.11, 3.12 and 3.13 all passed.

## Why I Chose This Issue

I started by building four `gh` searches around the kind of work I could actually take on. My scout looked for issues with no assignee that had been updated in the past 60 days, kept one issue per repo, and ranked them by the fewest comments. My third scan that day, on October 25, was the one that finished and returned 345 results across 80 repos, and my rubric checked the top 5. It accepted 4 and rejected 1, which was a good call because the rejected Agentboxx #12 already had the owner's PR #13 closing it. My first choice was ESCOMP/CTSM #2313, but after looking at the work I realized it was too big for the evening blocks I had available. I wrote a claim for that issue but never posted it or forked the repo.

On November 3 I ran another scan that found 361 results, with 114 left after filtering, and I picked `lunarstack-co/agentling` #21 by hand from the top 45. My scout had not graded it when I made that choice, which is something I want to be clear about. I ran the grade later on November 8 using the November 3 snapshot, and it came back ACCEPT with all 4 required checks passing and the difficulty marked straightforward. The issue fit because it involved Python, was a documentation correction, could be reproduced offline without a GPU, and the maintainer gave the file, line, steps and a "Done when" list. I also learned that my search method still needs work. Ranking by fewest comments favors issues that might be only a few hours old or sitting in tiny repos. I wrote a patch to prefer "good first issue" or sort more carefully by update time, but I have not applied that patch. The rubric itself did change in Unit 6, from `scope-fits` to `scope-feasible` with a difficulty note. Next time I want the scout to grade my actual pick before I claim it.

**Claim comment:** https://github.com/lunarstack-co/agentling/issues/21#issuecomment-6621700382

**Toolkit**

- **Tool:** My issue scout using `gh` and the course rubric.
- **What it produced:** Two scans, a useful early rejection, and a later ACCEPT verdict for #21 with 4 of 4 required checks passing.
- **Difficulties:** The search order favored very new issues, and I picked #21 before the scout graded it.
- **What changed:** In Unit 6 I changed the scope check to `scope-feasible` and added difficulty. The ranking patch is written down but still not applied, and next time I will grade before claiming.

## Understanding the Issue

**Problem Description**

The issue was a wrong explanation in the error table in `README.md` around line 325. The row said the loop raises `ModelOutputError` for an unparseable tool call, but that is not what the code actually does. Someone reading that might try to catch `ModelOutputError` and expect it to handle this specific failure, which would not work.

**Expected Behavior**

The README should explain what happens when the model sends a tool call that cannot be parsed. The loop asks the model to correct the call and gives it another chance. If the model keeps producing the same kind of bad call, the loop raises `ModelError`. The table should describe that behavior instead of naming the wrong exception.

**Current Behavior**

In `src/agentling/agent.py`, around lines 480 to 495, the loop can send a correction up to `_MAX_MALFORMED_RETRIES` times, which is 2 retries after the first failure. That means 3 model calls before it raises `ModelError` for repeated bad tool arguments. If the problem comes from the output limit cutting a call short, the loop raises `ModelOutputTruncatedError` instead.

**Affected Components**

The line that needed fixing was in `README.md`. I read `src/agentling/agent.py` to find the real retry behavior and `src/agentling/errors.py` to understand the error classes. `ModelOutputError` is used for output that cannot be used, such as `provider_data` that is not JSON-serialisable. I also checked `tests/test_readme.py`, which reads the table, and `REFERENCE.md`, which did not repeat the wrong claim.

**Toolkit**

- **Tool:** Claude Code to read the issue and follow the error path in `agent.py`.
- **What it produced:** A clear view of when the code raises `ModelError` and `ModelOutputTruncatedError`.
- **Difficulties:** I did not run into a problem with this part.
- **What changed:** Nothing in the code. This step showed that the documentation was wrong, not the retry logic.

## Reproduction Process

**Environment Setup**

I used Windows 11 with Python 3.11.15 in the virtual environment and `uv` 0.11.32. I forked `lunarstack-co/agentling` to `bs-gitem/agentling`, cloned it, ran `uv sync`, and used `main` at commit `d78e690` as the starting point. Before changing anything, the Ruff checks, formatting check and mypy passed. The test suite gave 537 passed, 6 skipped and 1 failed. That failure was `test_parallel_timeout_keeps_other_results`, which also failed on `main` in 3 out of 3 runs because its 0.01 second timeout is shorter than the roughly 15.6 millisecond Windows timer tick.

**Steps to Reproduce**

1. Run `uv sync` in the cloned repository.
2. Make `scratch/repro_21.py` using `agentling.testing.ScriptedModel` with four scripted turns. Each turn sends a `Delta` containing `ToolCallDelta(index=0, id="c1", name="add", arguments="{")`, which gives the loop an unfinished tool argument.
3. Run `uv run python scratch/repro_21.py` and print the exception class, module, message and number of calls.
4. Run `grep -n ModelOutputError README.md REFERENCE.md` to find where the incorrect explanation appears. It only appeared in `README.md` at line 325.

**Reproduction Evidence**

The script raised `ModelError` from `agentling.errors` with the message `Model produced unparseable tool calls on 3 consecutive turns: Invalid tool call arguments: '{'` and reported 3 model calls. I first ran the reproduction on November 3 and ran it again myself in PowerShell on November 8, with the same result. That showed why the README's original wording was misleading.

**Posted reproduction result:** https://github.com/lunarstack-co/agentling/issues/21#issuecomment-6649485163

**Commit showing reproduction:** None. `scratch/repro_21.py` was a scratch file I kept out of git on purpose, so I do not have a reproduction commit to link. Commit `9758d5e` is the documentation fix, not a reproduction commit.

**Toolkit**

- **Tool:** `repro-check`, run on November 8 after the PR was already merged.
- **What it produced:** REJECT. My posted comment was missing the OS, Python version and commit information, did not give a command someone else could follow, and did not meet all of the repo's bug report instructions. The claim itself passed.
- **Difficulties:** I ran the check after posting instead of before, so it could only tell me what I had left out.
- **What changed:** I put the full environment, steps and results in this story. Next time I will run `repro-check` before I post the reproduction.

## Solution Approach

**Analysis**

After looking through the code and running my test, I knew the problem was in the README and not in the loop itself. When `agglomerate_deltas` runs into an unparseable tool call, the loop handles it by asking for a correction and retrying. It raises `ModelError` after the allowed retries, or `ModelOutputTruncatedError` when the output was cut short. Changing the program would be fixing something that was already working.

**Proposed Solution**

My plan was to edit only the middle column of the `ModelOutputError` row in `README.md`, keeping the first sentence and the Retryable column the way they were. The replacement says: "The output can't be used, such as `provider_data` that isn't JSON-serialisable. For an unparseable tool call the loop asks the model to fix it, then raises `ModelError` if the model keeps failing." I was not changing `agent.py`, `errors.py`, the `ModelOutputTruncatedError` row, or the Windows timing test. I also did not add a CHANGELOG entry because the repo asks for one when users would notice the change, and this was correcting a sentence in the documentation.

**Implementation Plan**

1. Reproduce the behavior using `ScriptedModel`, so I know what exception actually comes back.
2. Search `README.md` and `REFERENCE.md` to see where the wrong claim appears.
3. Correct the one row in `README.md` without changing the rest of the table.
4. Run the repo checks from my manifest and then the ship-gate before making the PR.

There were no deviations from that plan. The final diff changed one line in `README.md`.

**Toolkit**

- **Tool:** `plan-check`, run November 8 on my `plan.md` after the PR merged.
- **What it produced:** REJECT. It passed 5 of 6 required checks but missed `test-observable`, and it also missed the preferred `risks-named` check.
- **Difficulties:** I did not post a separate plan comment, so the checker used my issue update as the stand-in, and I ran it too late.
- **What changed:** Next time the plan will say what someone should actually see, including `ModelError` after 3 calls, and what should happen when I deliberately change the Retryable cell in the README test.

## Testing Strategy

I tested the actual behavior before and after the edit, but because my change was only documentation, I expected the program's output to stay the same. That is what happened. The important part was making sure the README now described the output correctly and that nothing else in the repo was affected.

- **Reproduction before and after:** `scratch/repro_21.py` raised `ModelError` after 3 model calls both times. I did not change the code, so that was expected.
- **Old wording:** After the edit, `grep -rn "raises it for unparseable"` found no remaining match.
- **README tests:** `uv run pytest tests/test_readme.py` returned 12 passed and 6 skipped. I changed the Retryable cell from `no` to `yes` as a check and got 1 failed and 11 passed. After restoring it, I got 12 passed again. This test checks that cell, not my new sentence, so it does not directly prove the new wording is correct. I did not add a test for the wording.
- **Repo checks:** `uv sync` exited with code 0. `ruff check` passed, `ruff format --check` reported 40 files already formatted, and mypy found no issues in 40 source files.
- **Windows test suite:** With `test_parallel_timeout_keeps_other_results` deselected for the documented timing issue, pytest reported 537 passed, 6 skipped and 1 deselected. The one excluded test had already failed on `main` before my edit.
- **Linux CI:** On November 5 the PR's full CI suite passed on Ubuntu with Python 3.11, 3.12 and 3.13.
- **Ship-gate:** On November 4 it passed the 5 repo checks and 6 PR precheck checks before the PR was created. A separate manual run printed a `UnicodeEncodeError` because of a check mark and Windows `cp1252` after the PASS result had already been decided. The hook uses UTF-8 and did not hit that print error. I left the manual command unchanged.

**Toolkit**

- **Tool:** Ship-gate with my `pr-precheck` rubric and manifest.
- **What it produced:** PASS on all 5 repo checks and 6 PR precheck checks, with a reason for each result.
- **Difficulties:** Early blocks when I ran it on `main` and with an apostrophe in a heredoc, plus the separate manual print error.
- **What changed:** I made it opt-in per repo and added the `claude.exe` fallback. I did not change the manual print command.

## Implementation Notes

**2026-11-03**

I ran the second scout scan and found agentling #21, which the maintainer had opened that afternoon. I forked and cloned the repo, checked the baseline, and wrote `scratch/repro_21.py`, which raised `ModelError` after 3 calls. The voice guard stopped my claim twice before I posted the corrected version at 10:25 PM. I also dropped my earlier CTSM #2313 idea because the scope was too big.

**2026-11-04**

I installed the ship-gate and PR monitor, wrote `plan.md` and `ship-manifest.md`, and fixed the global hook and `claude` PATH problems. After blocks on `main` and an apostrophe in a heredoc, I worked on `docs/readme-model-output-error` and made the one-line README change. I committed it as `9758d5e` at 10:47 PM with the message `docs(readme): say the loop raises ModelError for unparseable tool calls`. The voice guard made me shorten my AI disclosure, the gate passed again at 11:06 PM, and I opened PR #37 at 11:07 PM. I posted my reproduction result on the issue at 11:13 PM, and the monitor drafted a wait note that I did not post.

**2026-11-05**

folathecoder approved the PR at 11:25 AM and merged it at 11:26 AM. Issue #21 was closed as completed, and the Linux CI checks passed. The PR had been open for about 12 hours.

**2026-11-08**

I ran the reproduction again myself and got the same result. I also ran `repro-check` and `plan-check`, which both returned REJECT, and ran the issue scout against my earlier pick, which returned ACCEPT. I wrote the 38-line `CLAUDE.local.md` after the merge, starred the repo as the maintainer requested, and worked on the story. My biggest wrong turn here was leaving the story and those checks until the last day instead of recording the work as it happened.

**Toolkit**

- **Tool:** Claude Code, my issue scout, voice guard, ship-gate, PR monitor and the course checks.
- **What it produced:** A reproduction, a one-line documentation fix, a passing PR gate, a merged PR and notes on what my late checks found.
- **Difficulties:** The PATH problem, early guard and gate blocks, and writing up the evidence later instead of as I went.
- **What changed:** I fixed the PATH handling for the hooks, narrowed the ship-gate to opt-in repos, and wrote down the missing evidence and improvements for next time.

## Pull Request

**PR link:** https://github.com/lunarstack-co/agentling/pull/37

I opened PR #37 on November 4 at 11:07 PM from `docs/readme-model-output-error`. The diff changed 1 file and 1 line in `README.md`, with no change to the Python code. My final AI disclosure in the PR was: "Drafted and checked with Claude Code; the run and the output are mine."

**Maintainer Feedback:**

- **2026-11-04:** There was no feedback yet. The monitor showed the PR as quiet and drafted a wait note, which I did not post.
- **2026-11-05:** folathecoder approved the PR at 11:25 AM with "Looks great. Can you optionally star the project to support?" and merged it at 11:26 AM. Issue #21 was closed.
- **2026-11-08:** I starred `lunarstack-co/agentling`.

**Status:** Merged

**Toolkit**

- **Tool:** Ship-gate, voice guard and PR monitor.
- **What it produced:** A PASS before `gh pr create`, a shorter AI disclosure that passed the guard, and a wait note instead of an unnecessary nudge.
- **Difficulties:** The monitor showed "no checks reported", and it was not clear at first why CI had not run yet.
- **What changed:** I found that the CI job had been created but was waiting for folathecoder to start it. Next time my playbook should check whether CI needs maintainer approval before treating that message as an issue.

## Learnings and Reflections

**Technical Skills Gained**

I got more practice reading Python code instead of trusting the documentation to be correct. I followed the exception handling in `agent.py`, used `ScriptedModel` to reproduce it without calling a real model, and checked the README against what happened. I also worked more with `gh`, `uv`, Ruff, mypy, pytest, CI and hooks that check work before it gets posted. This was a small change in one line, but I still had to prove what that line should say.

**Challenges and How I Handled Them**

One problem was that `claude` was not on my PATH, and it affected the voice guard, ship-gate and PR monitor until I added a fallback to the desktop app's `claude.exe`. My Windows baseline also had a timing test that failed before I made the change, so I documented that instead of pretending everything passed. The guard rejected my first claim and AI disclosure, and I rewrote them to meet the rules. I also learned from the REJECT results on my reproduction and plan checks, even though I ran them after the work was already merged.

**What I'd Do Differently Next Time**

Next time I would log notes as I go, because with my ADHD I forget things, and write the story as I go instead of leaving it for the last day. I waited until November 8 to run `repro-check`, `plan-check` and the scout grade, even though the PR was already merged, and that left me finding mistakes I could have caught earlier. I would also make sure the scout grades the issue before I claim it, fix a tool problem like the `claude` PATH issue once instead of running into it across three tools, and check why a PR says "no checks reported" before assuming something is wrong.

**Toolkit**

- **Tool:** `repro-check`, `plan-check`, my issue scout and Claude Code.
- **What it produced:** Two REJECT results that showed what I had left out, and an ACCEPT grade for #21 after the PR merged.
- **Difficulties:** I ran those checks late, and the `claude` PATH problem affected more than one tool.
- **What changed:** Next time I will keep notes as I go, check my work before posting, and grade the issue before claiming it.

## Resources Used

- [agentling Issue #21](https://github.com/lunarstack-co/agentling/issues/21): The maintainer gave the file, line, steps and a "Done when" list.
- [agentling `CONTRIBUTING.md`](https://github.com/lunarstack-co/agentling/blob/main/CONTRIBUTING.md): I used the setup commands, test rules, Conventional Commit format and CHANGELOG guidance. My manifest came from this file.
- `src/agentling/agent.py` and `src/agentling/errors.py`: I used these to understand the retry logic and which exception actually gets raised.
- `tests/test_readme.py` and `agentling.testing.ScriptedModel`: I used the test to check the README table and the scripted model to reproduce the behavior without a real model.
- folathecoder: The maintainer who reviewed and merged PR #37 and asked me to star the project.
- CodePath AI301 Unit 5, 6, 7 and 8 starter materials: I used them for the issue scout, voice guard, ship-gate and PR monitor.

**Toolkit**

- **Tool:** The agentling issue, `CONTRIBUTING.md`, the source and test files, `ScriptedModel` and the CodePath starter tools.
- **What it produced:** The issue scope, setup and test commands, a way to reproduce the real behavior, and the checks I used before the PR.
- **Difficulties:** The README and the code did not agree, so I had to verify what actually happened instead of going by the table.
- **What changed:** I used those resources to correct one README row without changing the Python code.
