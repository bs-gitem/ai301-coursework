# Voice guide: how I talk upstream

<!-- Drafted 2026-09-26 in Bailey's voice from his Unit 1 selection notes and the lecture's
slide-12 moment; Bailey edits any line that does not sound like him before posting. -->

## Who I am in threads

I am a first-time open-source contributor and a working college student; this is my first repo and my first issue. I am here to reproduce one bug faithfully, show my work, and then try a fix in a later phase. Readers can expect exact commands, real output, and plain statements of what I saw and what I did not. I use AI tools (Claude Code) to draft and check my work, and I say so.

## Rules I write by

### Rule: Name the thing, not the feeling

Every comment names the specific symptom or trigger from the issue in my own words. If a sentence could be pasted on a different issue, it is not done.

- Wrong: "I'd love to take this one! Happy to help however I can."
- Right: "Claiming #54: section headers are missed when the line starts with spaces or a tab. I'll reproduce it on the current main with a two-line resume and post the output here."

### Rule: Promise the next step only

I promise what I will do next (reproduce, investigate, report), never a fix, a date, or a guarantee. The report earns the next promise.

- Wrong: "I'll have a PR up by Friday, this looks like an easy one."
- Right: "Next I'll reproduce it and post an environment record, the exact input, and what the parser returned."

### Rule: Show it before I say it

No conclusion goes above an artifact that backs it. If I did not see it in my own output, I do not write it. "I think" is allowed; "definitely" is not, unless the output is right there.

- Wrong: "Confirmed, this is definitely the whitespace regex in section_detector.py."
- Right: "With `  EXPERIENCE` (two leading spaces) the detector returned 0 sections; with `EXPERIENCE` it returned 1. Output below. I have not looked at the cause yet."

### Rule: Cannot-reproduce is a result, not a failure

If it does not reproduce, I say so, record my environment, and name what differed from the issue. I do not stretch an adjacent symptom to fit.

- Wrong: "Got a different error but it's basically the same thing, so reproduced."
- Right: "Could not reproduce on Windows 11 / Python 3.13 / main at 9e2f1c: the detector found the section. Differences from the issue: reporter is on Linux and Python 3.11. Next I'll try a tab instead of spaces."

### Rule: Say the AI part out loud

When the repo asks, and by default on Path Review, I state that I used Claude Code to draft or check the comment. One short sentence, no apology.

- Wrong: (nothing)
- Right: "Drafted and checked with Claude Code; the run and the output are mine."

## Things I never post

- "+1", "same here", "any update?", or a claim with no stated next step.
- A deadline, a guaranteed fix, or "easy fix".
- A root cause I have not shown in output.
- "Same as above, can confirm" on a classmate's repro (Path Review house rule: my proof goes up in my own words).
- Anything written at 2am that I have not re-read once in the morning voice.
