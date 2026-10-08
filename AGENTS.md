# AGENTS.md — layer-podcast-audio

Standalone candy repo owning the `podcast-audio` candy (plus its `skill:` entity, projected
into the marketplace corpus), the `podcast-audio-app` box that composes it, the
`scripts/podcast-render` renderer the candy installs, and the `check-podcast-audio-pod`
disposable R10 bed. The entities live in `charly.yml` at the repo root.

Canonical files:

- `charly.yml` — the `podcast-audio:` candy, the `podcast-audio-app:` box, the
  `check-podcast-audio-pod:` bed, and the `podcast-audio-skill:` skill entity.
- `scripts/podcast-render` — the renderer itself (a POSIX shell script; a candy's build
  context is the directory holding its `charly.yml`, so it sits beside it under `scripts/`).
- `.github/workflows/tag-on-merge.yml` — CalVer tag + `CHANGELOG/` on merge.
- `README.md` — user overview only; never agent guidance.

## Load these skills first (R0)

- `/charly-image:layer` — the candy authoring reference (`charly.yml` schema, plan-step verbs).
- `/charly-check:check` — the check/bed model and the check-verb catalog. Load before
  changing any `check:` step or the bed.
- `/charly-internals:git-workflow` — before any git/PR action, and before touching the
  `require:` pin.
- `/charly-tools:podcast-audio` / `/charly-tools:kokoro-tts` — the owning skills.

## Build / validate / test

- `charly box validate` — the structural pre-flight; it must report **0 warnings, 0 errors**.
- `charly check run check-podcast-audio-pod` — **the R10 gate** for this repo: image build →
  check-image → deploy → check live → fresh rebuild → teardown, on a `disposable: true` pod.
  The candy's self-test render runs in BOTH contexts, so it executes at `charly check box
  podcast-audio-app` (no deploy) and again, live, inside the deployed pod.
- The merge gate is the org-wide `charly/pr-validator` (required check `validate / validate`);
  this repo carries no per-repo candy gate.

## Modify this repo

- **The `require:` pin is a MERGED ref, never a branch and never a hand-pin.** It names the
  `layer-kokoro-tts` release tag that actually exists on that repo's `main`. Advance it only
  once the producing PR has merged and its CalVer tag exists.
- Edit the `podcast-audio:` candy AND its `podcast-audio-skill:` entity together: the skill is
  the projected usage source, so a cast, flag or manifest change not mirrored there leaves the
  corpus stale.
- The cast is a table (`NAME<TAB>SID<TAB>VOICE`), not a schema: keep `--voice` generic for any
  speaker name. New behaviour claims belong in the `plan:` as an observable `check:` step, and
  in the skill.
- **A `check:` step must NOT name a candy-declared variable.** Its fields resolve only the
  auto-exports (`${HOME}`, `${USER}`, `${ARCH}`); naming `PODCAST_TTS_ENGINE` (or any candy
  `var:`/`env:` entry) makes the step report **SKIPPED** — `unresolved variables:` — which does
  not fail the run. Measured 2026-10-08: the same mistake disarmed six checks on the
  `layer-kokoro-tts` bed while it still reported PASS. Pass the literal path, or let the
  renderer fall back to its own env contract (as `podcast-render-self-test` does).

## Landing

- PR-only. No CHANGELOG file is staged in a PR: the PR body IS the changelog, and
  `tag-on-merge` writes `CHANGELOG/<merge-time CalVer>.md` from it at merge time. The
  authoritative rulebook is the umbrella `AGENTS.md` in `opencharly/opencharly`.
