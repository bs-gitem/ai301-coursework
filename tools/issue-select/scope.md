# Scope: where to look, and who is looking

<!--
This file is the skill's field of view. The rubric (rubric.md) decides
whether an issue is GOOD; the scope decides which issues are candidates
at all, and whose hands the issue would land in. It applies in live mode
only: in eval mode the bundle is the whole world and this file is
ignored.

Two parts. Staff wrote the first; you write the second.
-->

## Where candidates come from

Only issues in the course's Path Review repository are candidates:

- Repo: `codepath/pathreview-ai301-fa26-s3`

Do not search, fetch, or grade issues from any other repository, however
promising. The wider GitHub comes later in the course; for now the field
is Path Review.

**Path Review house rule.** Path Review is a classroom, and your
classmates are not strangers. Ignore the usual claim signals here: other
students' claim comments (and there may be several on one issue) do not
block an issue, and finding some on the issue you want is normal. Claim
anyway: course credit attaches to the pull request you open, not to
whether it merges, so a shared issue costs nobody anything. Everything
else in the rubric applies as written.

## Your fit profile

<!-- YOU write this part: a few sentences about you. What languages and
tools you have actually used, what you want to get better at, anything
you want to avoid. The skill uses this only to RANK the issues your
rubric accepts, never to change a verdict: fit cannot rescue an issue
your rubric rejects, and cannot sink one it accepts. -->

Python is the language I actually write: machine-learning coursework (clustering, matrix factorization, a book-recommendation project) with pandas, NumPy and scikit-learn, some TensorFlow/Keras, and an NLP class this term (tokenizing, regex, text extraction). I run my own company's stack as an operator, not a developer: an Ubuntu VPS, Docker Compose, Postgres, Bash, cron jobs and a LiteLLM model gateway, so I can read logs, configs, Dockerfiles and YAML, but I have not written production code in those systems. I use git and GitHub every day through Claude Code; driving them by hand is new, and this course is where I want that to become routine. I want to get better at reading a codebase I did not write, reproducing a reported bug, and shipping a small, tested Python change with a clean pull request. Rank Python issues first, then docs, config or shell-script issues in repos I can run locally without a GPU. Avoid Java, Kotlin/Android, Go, Rust, C/C++, React or TypeScript front-end work, Kubernetes/Terraform, and anything that needs a paid service or a long build. I work in two- to three-hour evening and weekend blocks, so an issue with a clear acceptance condition and reproduction steps fits me better than an open-ended one.
