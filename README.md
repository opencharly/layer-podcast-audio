# layer-podcast-audio

Turn a two-host episode SCRIPT into audio. `podcast-render` parses speaker-tagged markdown, synthesises each cue in its own voice, assembles the segments with real pauses, loudness-normalises, encodes WAV + AAC/M4A, and emits a per-segment manifest.

Nothing else in the org renders a **dialogue**: the TTS candies synthesise one utterance in one voice, and ffmpeg encodes. This is the candy that turns a conversation into an episode.

## The cast

`podcast-render` carries a cast table of `NAME<TAB>SID<TAB>VOICE` rows. The shipped default cast is the news desk's deliberate pair:

| NAME | SID | VOICE | |
|---|---|---|---|
| `CONCH` | 1 | `af_bella` | US female |
| `TEMPER` | 6 | `am_michael` | US male |

`--voice NAME=SID` adds or overrides a row for **any** name, so a script may name one speaker or twenty. A row given without a `VOICE` column is labelled from the model's own voice table (the `speaker2id` mapping `/charly-tools:kokoro-tts` publishes).

## Status

Newly created (2026-10-08) to close a measured gap. See `charly.yml` for the candy
entity, its `plan:` acceptance checks, the `podcast-audio-app` box and the
`check-podcast-audio-pod` R10 bed.

## Verify

```bash
charly box validate                       # the manifest parses and validates: 0 warnings, 0 errors
charly check box podcast-audio-app        # the candy's checks against the built image, no deploy
charly check run check-podcast-audio-pod  # the R10 gate: build -> check -> deploy -> live render -> rebuild -> teardown
```

The candy's own `check:` steps are the executable acceptance: the self-test renders a real
two-line dialogue and asserts the WAV, the M4A, the manifest, the cast and the pause, so it
can fail. It runs in both the build and runtime contexts, so the disposable pod bed re-proves
the same render live inside a running candybox.

The engine, the multi-voice model and ffmpeg arrive through `require:` — the pinned
`layer-kokoro-tts` release (merged tag only), `layer-sherpa-onnx`'s engine and `layer-ffmpeg`.
