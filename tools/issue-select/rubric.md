# Rubric: is this a good first issue?

<!--
Drafted 2026-09-22 from Bailey's three worksheet checks (repo-alive, scope-fits, nobody-on-it)
plus one policy check, after grading calib-01..04. Bailey reviews and edits; the judgment is his.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| repo-alive | repo-facts block: "archived", "last push to any branch", "last 5 default-branch commits", "maintainer first-response sample" | repo is not archived AND last push is 90 days or less before the capture date AND at least 1 of the last 5 default-branch commits is by a human author (not a name ending in [bot]). Human commits alone prove life; the response sample is consulted only when all 5 commits are bots, and then at least 1 sampled issue must show a maintainer comment within 30 days. An empty or slow response sample does not fail a repo with human commits | required |
| scope-fits | issue body plus the full comment thread | the issue describes one concrete bug, docs change, or feature with a stated result (a page, file, function, or observable behaviour) that a newcomer could finish in one pull request. Short maintainer-filed bugs pass; optional "additional suggestions" or follow-up ideas do not widen the scope. FAIL only when the issue is a tracking list, umbrella, or megaissue spanning many files; a one-line wish with no spec and a product decision hiding inside; or a design debate a maintainer declined or that has run 1 year or more without a settled spec | required |
| nobody-on-it | repo-facts "this issue: assignees / linked PRs" line plus the full comment thread | no assignee AND no open pull request (formally linked or cited by number in a comment) AND no "I am working on this" comment within 90 days that a maintainer has not called stale | required |
| policy-allows | repo-facts "contribution policy" line | the policy does not ban AI-assisted contributions outright; a policy that allows assistive AI with the contributor taking responsibility passes | required |

## Verdict rule

accept if every required check passes; unclear counts as fail; preferred checks never change the verdict.
