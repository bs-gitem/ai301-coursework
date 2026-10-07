# Evidence guide: where evidence lives in a plan package

<!--
Written by Bailey Souza (bs-gitem) for AI301 Unit 3, 2026-10-07, with Claude Code as
the drafting assistant, next to calib-01 (lazygit#5900) and pkg-20 (ghostty#11261) so
every "where" below names a real block in a real package. This is a new map: a plan's
evidence does not live where a repro's did. In an eval bundle the blocks are, in order:
header, "## Repo facts", "## Issue", "## Thread highlights", "## Repro evidence",
"## Candidate plan", "## Candidate plan comment". In live mode the same families live on
the GitHub issue thread, in the repo's docs, and in the student's two draft files.
-->

## Diagnosis and grounding

**Where it lives.** Eval bundle: the plan's cause is the first sentence or the "Cause:" / "Diagnosis:" / "What I found" line of `## Candidate plan` (calib-01: "Cause: after a push started from the branch-commits view, that view's model is not refreshed"). The behavior that cause must explain is in `## Repro evidence`: the numbered Steps, the fenced Artifact block, any line starting "Control" or "Control runs", and the "Expected:" and "Actual:" lines at the end. Live mode: the cause is in the student's draft `plan.md`; the behavior is in the student's own posted repro comment on the issue thread (or the house repro pack as quoted in the drafts), read with `gh issue view <n> --comments`.

**What good looks like.** The stated cause names behavior the repro actually shows and survives every control run: if the control removed a flag, a hyperlink start, or a pager and the symptom vanished or stayed, the cause must agree with that. Good: "the stale `prev` pointer survives the capacity change" when the no-hyperlink control passes. Bad: blaming a tokenizer when the control run with the same items and no flag parses fine, or calling the repro's version difference "a red herring" with no run behind that word.

## Scope

**Where it lives.** Eval bundle: the "Scope" paragraph or the "In:" / "Out:" / "In scope:" / "Not in scope:" lines of `## Candidate plan` (calib-01: "In: the push completion callback in `pkg/gui/controllers/sync_controller.go` ... Out: any change to how push status is computed"), plus the full list of approach steps, which is where creep hides when the scope line is clean. Compare against the symptom in `## Issue` (title and body). Live mode: the "Scope" section of the draft `plan.md`, read against the issue body on GitHub.

**What good looks like.** One bounded change: every step acts on the issue's symptom, and anything nearby is either untouched or named out of scope with a reason ("defers the direct-globset rework because it cannot be tested on this machine"). A drive-by rewrite reads differently: the fix is step 1 and steps 2 to 5 are a migration, a new setting, a UI rework, a CI matrix, or "while I am in there". The issue's own words set the boundary; a good plan can be read against the issue title alone and nothing is left over.

## Executability

**Where it lives.** Eval bundle: the "Files", "Change", "Approach", or numbered-steps part of `## Candidate plan` (pkg-20: "1. Add a monotonic generation counter to the page ... 2. In `Terminal.print`, snapshot the generation next to `prev` ... 3. Add both of the issue's test cases"). Files may also be named inline in the cause or scope sentence. Live mode: the "Files" and "Approach" sections of the draft `plan.md`.

**What good looks like.** A stranger with the repo cloned could open the named file and make the first edit without asking the author anything: at least one concrete file, module, function, or doc, and one chosen approach in a stated order. Open questions are fine when a default is picked ("per-use check now; move it to the two growth-adjacent sites if the benchmark shows cost"). Bad: "profile the git modules and optimize what shows up", "recover() somewhere", "gocui? tcell? not sure", "upstream or vendored, whichever is easier".

## Test plan

**Where it lives.** Eval bundle: the "Test:" / "Test plan:" line or section of `## Candidate plan` (calib-01: "repro steps above; at step 3 the color must flip without leaving the view"). Map it onto the Steps and Artifact in `## Repro evidence`: which step is re-run, and what the artifact should now show. Live mode: the "Test plan" section of the draft `plan.md`, read against the commands and output in the student's posted repro comment.

**What good looks like.** A decisive test plan names the observable: a value (`['Education', 'Skills']` instead of `[]`), an exit code (0 instead of 2), a named test going from fail to pass, a color flipping at a named step, a measurement under a stated number. It usually says "re-run the repro steps; expect X at step N". A vague one names only a process or a feeling: "run the full test suite", "should feel fast", "nothing else should feel broken".

## Honesty

**Where it lives.** Eval bundle: the "Risk", "Risks", "Unknowns", "Open question", or "flag" sentences of `## Candidate plan`, usually at the end (pkg-20: "Risk, stated: I have not yet measured the per-print cost ... Whether other cached pointers ... is an open question"), and any "Deviations" or "what changed" note. Also read the cause and comment for confidence words ("definitely", "clearly", "red herring", "will have a PR up by Friday"). Live mode: the "Risks and unknowns" and "## Deviations" sections of the draft `plan.md`; after a build, the Deviations section is where an honest mid-build change is recorded, and the skill re-grades the whole package with it in.

**What good looks like.** Stated unknowns are named as unknowns with what would resolve them ("not yet measured; if it shows in the benchmark I will..."). False confidence asserts a fact the repro never produced, or promises a date or an outcome before the work. A plan that says "I see no risk beyond the one site" for a one-line change is honest too; the thing to catch is certainty with nothing behind it.

## Comms

**Where it lives.** Eval bundle: `## Candidate plan comment` is the words that would be posted; read it against two blocks. First, `## Thread highlights`: each bullet is "date login (ROLE): what they said"; OWNER, MEMBER, CONTRIBUTOR, and COLLABORATOR bullets that isolate a culprit, propose or reject an approach, request testing, mark something works-as-intended, or point at a prior PR are maintainer direction (pkg-20: mitchellh proposes recomputing `prev` only when capacity changed). Second, `## Repo facts`: the "bug reports:" line (template asks) and the "contribution policy" line, which holds the AI-policy wording (pkg-20: "All AI usage in any form must be disclosed, stating the tool used and the extent"). Live mode: `gh issue view <n> --comments` for the thread; the repo's `CONTRIBUTING.md`, `AI_POLICY.md` if any, `.github/PULL_REQUEST_TEMPLATE.md`, and issue templates for the conventions; the student's draft `comment.md` for the words.

**What good looks like.** Thread-aware: the comment names the direction it follows or says why it departs ("my plan follows the direction proposed here: a page generation counter"), engages a prior-art PR instead of racing it, and acknowledges a works-as-intended note. Boilerplate ignores the thread and could be pasted on any issue. On disclosure: when the policy says AI use must be disclosed, the comment names the tool and the extent in a sentence; when the policy only asks for the author's own words or a human in the loop, a specific, first-person comment satisfies it with no disclosure sentence; no policy asks nothing.
