# Validation of the boilerplate skill rewrite

Validated on 2026-10-11 against the two supplied ZIP snapshots.

## Passed

- Brain's unmodified `scripts/validate_skills.py`: all 29 skills passed.
- All 78 recognized Luau code fences compiled with Luau 0.731, the version used by the repository's CI. This checks syntax, not Roblox-aware types or runtime behavior.
- The 32 rewritten files obey the entrypoint/reference size limits, frontmatter requirements, cross-skill names, local-reference rules, and fenced-code conventions.
- Regression suite: 38 of 39 tests passed; identical result in the untouched input ZIP on this Windows host.
- Unchanged source files were compared byte for byte with the original Brain ZIP. No validator or test implementation was changed.

## Existing Windows regression failure

`test_api_drift_main_annotates_mirror_snapshot_without_fetching` fails identically
in the original ZIP and adapted tree. Its fixture writes text using Windows
newline conversion but hashes the LF byte sequence, producing a snapshot hash
mismatch and suppressing the expected date banner. The companion hash-mismatch
tests pass. This pre-existing fixture was not changed as part of the skill rewrite.

## Supporting changes

The API drift registry was synchronized with the rewritten teaching files:
36 checks remain; 48 claims are no longer taught, and 62 obsolete file
associations were removed. Checks for unchanged skills and still-taught claims
remain. This is a content-scope adjustment, not a claim that the retired APIs
are invalid. Live API drift and source-URL checks were not run. Existing
`last_reviewed` dates were preserved; local boilerplate inspection is identified
separately in every rewritten reference.

The README points install commands to the user's fork and describes the adapted
scope. MIT attribution and the boilerplate's Unlicense notice are included.

## Not run

- Live Creator Hub API drift, source URL reachability, and tool-version checks.
- Skill-host installation or behavioral testing in an active agent session.
- Boilerplate `check.ps1` type/lint/build checks: no game source changed, and its packages are not installed in the extracted snapshot.
- Studio playtests or visual verification of authored assets: no Studio connection was used.

The examples are explicitly illustrative. Validate their integration in the
real game project with its current packages, source contracts, static checks,
and feature-specific Studio tests before shipping game behavior.
