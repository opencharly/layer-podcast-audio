# Changelog

**This `CHANGELOG/` directory is this repository's home for historical content.**
Every repository in the project keeps its **own** `CHANGELOG/` — history is
repo-scoped, never centralized in one file, and split into one file per CalVer
release version so no single file grows without bound.

`README.md` and `AGENTS.md` describe the
**current** state of this repo — present tense, forward-looking. Any
reference to a previous version, a past rename, a completed cutover or
migration, a relocated / deleted / retired identifier, a "previously /
formerly / was / no longer", a dated change note, or a commit-referenced
cautionary tale belongs **here** and nowhere else.

## Layout

- **One file per CalVer release version:** `<YYYY.DDD.HHMM>.md` (e.g.
  `2026.234.0347.md`). The CalVer is minted once per landing, at **merge** time,
  and the same stamp names both this file and the repo's `v<YYYY.DDD.HHMM>` tag.
- **No CHANGELOG file is staged in a PR.** The PR body IS the changelog: the
  org-wide `tag-on-merge` workflow writes `CHANGELOG/<merge-time CalVer>.md` from
  the merged PR's title + body. The org-wide `charly/pr-validator` neither
  requires nor renames a changelog file — its own rule 18 says "A CHANGELOG file
  in the diff is neither required nor expected". An author-written entry would
  carry an author-time name that can never match the merge-time stamp.
- The entry is the merged PR's title + body, so it states what changed from the
  reader's perspective by construction.

## Reader's guide

- Want the current contract? Read `README.md` + `AGENTS.md`.
- Want what changed in a snapshot? Read the newest `YYYY.DDD.HHMM.md`.
