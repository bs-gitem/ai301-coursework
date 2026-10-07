# Plan for #54: Resume section detection fails with leading whitespace

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/54

This plan is based on my own repro against commit `2f4e82f`, posted on the issue on 2026-09-30.

## Diagnosis

The problem is in `_detect_sections()` inside `ingestion/parsers/resume_parser.py`. It tries four regex patterns for each section name. All four expect the section name directly after `^` or `\n`.

That means something like:

```text
Education:
```

works, but:

```text
    Education:
```

doesn't.

I reproduced it on a fresh clone of `main`:

```text
Python 3.13.14
OS Windows-11-10.0.22631-SP0
INDENTED []
CONTROL ['Education', 'Skills']
```

`INDENTED` is the input from the issue unchanged. For `CONTROL`, I used the same text and only removed the four spaces before the section headers. Both Education and Skills were found after that.

So the indentation is the part that matters here. Nothing else changed between those runs. That also makes me pretty confident this isn't coming from the section-name list, `.lower()` or the `[:|-]` suffix handling.

The three tests named in the issue fail the same way when I run them with `--runxfail`:

```text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_single_column_resume_text
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_parse_resume_no_work_experience
FAILED tests/unit/test_resume_parser.py::TestResumeParser::test_detect_sections
3 failed, 7 deselected, 2 warnings in 0.22s
```

All three use indented triple-quoted text and expect a section to be detected.

## Scope

For this fix I'm changing:

- The four regex patterns inside `_detect_sections()`
- The three strict `xfail` markers for the tests named in the issue

The `xfail` markers need to come off after the fix. They're strict, so once the tests start passing they would turn into `XPASS(strict)` and fail CI anyway.

I'm not changing:

- `_strip_markdown()`
- `test_parse_markdown_resume`
- `test_strip_markdown_syntax`
- `SECTION_HEADERS`
- The order of `detected_sections`
- PDF extraction
- Anything else in the parser

The two markdown tests also carry a `#54` marker but I don't think they belong in this change. Their problem is that `^#+\s+` doesn't strip an indented `# Header`. That's in `_strip_markdown()` and it's a different failure than `_detect_sections()` not finding an indented section.

I saw some of the other plans include both. I'm keeping mine focused on the actual section detection issue. If the maintainer wants the markdown fix in the same PR I can add that as a separate commit.

## Files

- `ingestion/parsers/resume_parser.py`: change the four patterns in `_detect_sections()`.
- `tests/unit/test_resume_parser.py`: remove the three strict `xfail` markers for the tests fixed by this change.

## Approach

Add `[ \t]*` right after the `^` or `\n` anchor in each of the four patterns.

Example:

```text
^[ \t]*education\s*[:|-]
```

I picked `[ \t]*` instead of stripping every line first because this keeps the fix small and directly addresses the thing thats failing. It allows horizontal whitespace before the header, but doesn't let the match cross into another line.

Then:

1. Rerun `repro54.py`.
2. Confirm the indented input now finds Education and Skills.
3. Run the three named tests with `--runxfail`.
4. Confirm all three pass.
5. Remove their `xfail` markers.
6. Run the full `tests/unit/test_resume_parser.py` file.
7. Make sure the two markdown tests are still `xfailed` and there is no `XPASS`.
8. Run the full unit suite and compare it against untouched `main`.
9. Run `ruff check`, `black --check` and `mypy` on the two changed files.

Branch: `fix/54-resume-section-whitespace`

Commit will use the normal Conventional Commits format, `fix(ingestion): ...`, with `Fixes #54` in the footer.

## Test plan

Before:

`INDENTED []`

Expected after:

`INDENTED ['Education', 'Skills']`

The control should stay the same:

`CONTROL ['Education', 'Skills']`

The three tests from the issue should go from failing under `--runxfail` to:

`3 passed`

For the full resume parser test file I expect:

- Before: `5 passed, 5 xfailed`
- After: `8 passed, 2 xfailed`, no `XPASS`

In the repro script I compare sorted lists because `detected_sections` is built from a `set`. I don't want the order changing between runs to look like a failure when it isn't.

## Risks and unknowns

**Tabs.** `[ \t]*` should handle both spaces and tabs. I haven't seen an actual PDF sample that comes out tab-indented though, so that part is based on the regex behavior and not a real PDF run.

**Order.** `detected_sections` is created from a `set`, so the order can switch between `['Education', 'Skills']` and `['Skills', 'Education']`. None of these tests depend on the order. I'm not changing that as part of #54.

**Markdown tests.** I expect the two markdown tests to stay `xfailed`. They're checking markdown stripping, not the section detection problem I'm fixing here. If one of them unexpectedly passes I'll have to deal with the strict marker before CI.

**Existing failures.** On untouched `main` I already have six failures in `tests/unit/test_review_service.py`. Those need a database that isn't available in my local unit setup. They were there before this change, so I'll compare the full run against `main` instead of treating any red test as something caused by this PR.

## Deviations

The plan ended up holding. I didn't need to change the scope.

On `fix/54-resume-section-whitespace` I added `[ \t]*` after the `^` and `\n` anchors in the four patterns. I also removed the three strict `xfail` markers.

The repro gave me:

`INDENTED ['Education', 'Skills']`

The three tests from the issue:

`3 passed`

The full resume parser file:

`8 passed, 2 xfailed`, no `XPASS`.

The full unit suite on untouched `main` was:

`6 failed, 369 passed, 53 xfailed`

After my change:

`6 failed, 372 passed, 50 xfailed`

The same six `test_review_service.py` tests were still the failures.

`ruff`, `black --check` and `mypy` all passed on the two files I changed.

I did not touch `_strip_markdown()` or the two markdown tests.
