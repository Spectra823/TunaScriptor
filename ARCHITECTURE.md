# Architecture

## Process model

```
┌────────────── GUI process (PySide6, light: no PyTorch) ──────────────┐
│ ui/  main window · library window · piano roll · cifrado editors     │
│ library/  SQLite store · autosave writer thread · file service       │
│ music/ cifrado/ export/ playback/  pure data + logic (unit-tested)   │
│ engines/  QProcess runner · readiness checks · install plan          │
└───────────────┬──────────────────────────────────────────────────────┘
                │ JSON request on stdin → JSON lines on stdout
┌───────────────▼────────────── engine process(es) ────────────────────┐
│ engine venv (uv): PyTorch, muscriptor, chordify, whisper, librosa     │
│ engines/workers/muscriptor_worker.py   check|login|download|transcribe│
│ engines/workers/chordify_worker.py     check|analyze|download_whisper │
│ engines/workers/melody_worker.py       check|extract (★ melodía)      │
└──────────────────────────────────────────────────────────────────────┘
```

Why: the GUI stays responsive and small to package; a crashing or
out-of-memory model cannot take the editor down; cancelling = killing the
process; the engine env can use a different Python than the GUI. Workers only
import the standard library and the engine packages (never `tunacifrado`).

Worker protocol messages: `status` (honest text), `progress` (MuScriptor's
real chunk counter, `completed/total`; no invented percentages), `result`,
`error` with a code (`auth_required`, `network`, `oom`, `audio_error`,
`missing_dependency`, `model_missing`, `disk`, `cancelled`, `crashed`).
Results are written to `*.part` and renamed on success.

## Two workflows

| | Guitar cifrado | Bandurria / laúd |
|---|---|---|
| Engine | Chordify (`detect_chords`) + Whisper (`transcribe_audio`) | MuScriptor (`TranscriptionModel.transcribe`) |
| Input | full song by default | full song |
| Analysis kind | `chords_lyrics` | `notes` |
| Output | `GuitarCifrado` (lyrics + chords anchored to characters) | `Transcription` → piano roll → `TabCifrado` per track |
| MuScriptor started? | **never** (verified by an integration test) | yes |

A notes analysis can also produce guitar charts from a harmony track
(`cifrado/harmony.py`), or guitar melody tablature.

## Data model and storage

`%LOCALAPPDATA%\TunaCifradoStudio` (Linux: `~/.local/share/TunaCifradoStudio`):

| Path | Content | Regenerable? |
|---|---|---|
| `library.sqlite3` | songs, analyses (+ note edits), cifrados, folders | **no — user work** |
| `audio/` | optional managed copies of imported audio | no (unless the original still exists) |
| `analyses/<id>/result.json` | immutable engine output ("original transcription") | expensive |
| `cache/` | previews, conversions, in-flight job files | yes |
| `engines/venv` | engine environment | yes (re-install) |
| `logs/` | rotating log, tokens redacted | yes |

Relationships: song 1─* analysis, song 1─* cifrado, analysis 1─* cifrado,
folder 1─* cifrado. Cifrado content is a self-contained JSON document, so
deleting a song or analysis **detaches** cifrados instead of deleting them.
Deleting a folder moves its cifrados out. Editing a cifrado never touches the
transcription it came from (conversion copies the notes).

Document formats (all versioned JSON): `tunacifrado.transcription`,
`tunacifrado.tab`, `tunacifrado.guitar`.

## Autosave and integrity

* Every completed edit submits an immutable snapshot keyed by document;
  a background writer coalesces per key (newest wins, order preserved) and
  writes 0.35 s after the last edit, in one SQLite transaction
  (WAL, `synchronous=FULL`).
* The status indicator says "Guardando…" until the writer confirms; "✓ Guardado"
  only after commit. Failures keep the snapshot pending, retry with backoff
  and show a discreet "Reintentar".
* Closing a window or the app flushes; unsaved changes trigger a choice,
  never silent loss.
* `session.lock` detects an unclean shutdown; on the next start the database
  is integrity-checked, running analyses are marked *interrupted*, half-written
  job files are removed. Valid data is never replaced.
* Guarantee: committed edits survive crashes/power loss; edits from the last
  fraction of a second may be lost.

## Undo

Snapshot commands (`ui/undo.py`): each step stores the document before/after.
One undo stack per editor window and one for the transcription.

## Notation module

`cifrado/` holds every musical rule, isolated from the UI:
`instruments.py` (tunings, range, laúd written offset), `fingering.py` (Viterbi
string/fret choice, "Tuna" rules, out-of-range flagging), `tab_model.py`
(structured notation, format v2), `tab_layout.py` (the ONE layout used by the
editor grid, the text view and the PDF), `tab_render.py` (text view),
`chords.py` (Spanish/English parsing, display styles), `guitar_model.py`
(lyrics, chords, voices/legend/colours/dynamics), `guitar_builder.py`
(chord↔lyric alignment), `harmony.py`, `convert.py` (track → cifrado: octave,
tremolo, spacing). See `docs/NOTATION_DECISIONS.md`.

## Startup and the development launcher

`run_dev.bat` runs `.venv\Scripts\python.exe -m tunacifrado` in the foreground
(console Python, so errors are visible). `app/main.py` starts logging before
importing Qt, reports Qt load failures with the likely cause, and installs an
exception hook. `setup_dev.bat` builds `.venv` with uv-managed Python (never
Anaconda) and runs `--self-test`.

## v0.3 additions
* `cifrado/legend.py` — legend model shared by guitar and tab cifrados (`VoiceLegendMixin`).
* `cifrado/sync.py` — `SyncMap`: bisect-searchable timeline (lines, word spans, chords) for guitar playback.
* `music/melody.py` — ★ melody tracks (`Track.role`), confidence filter, combination; `melody.json`
  is stored beside `result.json` and merged by «Restaurar original».
* `library/images.py` — image library (`images/`, table `images`) and cover art (`covers/`); schema v2.
* `export/interchange.py` — native / MusicXML / ChordPro export+import (`docs/EDITABLE_EXPORT.md`).
* Tab editor grid: strips (`_Band`) with precomputed glyphs; paint only what intersects the exposed
  rectangle; selection repaints two small rectangles; hit-testing bisects to one strip.
* Player: 30 ms `tick` while playing, pending seek until media is loaded, `play_range`.
* Theme: `ui_scale` multiplies all style-sheet font sizes and the app font; custom-painted widgets use
  `theme.pt()`.

## v0.4 additions
* `engines/workers/melody_worker.py` — ONE principal melody: sung passages (hysteresis over voice-like
  contours) + instrumental lead (iterative harmonic subtraction → Viterbi over pitch candidates) + note
  segmentation with vibrato tolerance and attack detection (`docs/MELODY_ANALYSIS.md`). melody.json v2
  adds `version` and `segments`.
* `music/melody.py` — single «★ Melodía principal» track (`m-main`); `migrate_legacy` turns the v0.3
  sung-notes track into an ordinary track (in memory, on load); an edited ★ track is kept hidden
  «(anterior, editada)» when detection runs again; `library/service.py` keeps the v0.3 melody.json as
  `melody.v1.json`.
* `playback/synth.py` — tone cache per pitch, RMS mastering + soft limiter (was peak normalisation:
  −42 dBFS with 16 tracks), `SYNTH_VERSION` in the cache key.
* `playback/player.py` — `rendering` state: Play / play_range pressed while a preview renders start when it
  is ready; `load_samples` plays an already-rendered buffer.
* `playback/guitar.py` — generated guitar accompaniment from a guitar cifrado: voicings from the chord
  symbol, chord timing (detected → word under the chord → interpolated → default), strum patterns,
  plucked-string additive synthesis. Used by the guitar editor (whole song on the song's time axis, or
  one line).
* `playback/tab_audio.py` — notes to hear from a bandurria/laúd tablature (sounding pitch from string +
  fret, tremolo strokes, simultaneous notes, unplayable entries skipped); per-section playback in the tab
  editor.
* Piano roll: `editable` set (any number of tracks), `active_track` = target for new notes, box selection
  across editable tracks stored in time/pitch coordinates, 4 px drag threshold, vertical autoscroll.
  Main window: a second Player for «▶ Notas seleccionadas».
* Tab editor: empty-slot model (`TabGrid.slot`), `add_note_at` (simultaneous or in a gap without moving
  neighbours), `set_fret_keep_layout`.
* `ui/dialogs.scrollable_body` — dialog content in a scroll area, buttons outside, ≤ 90 % of the screen.

## v0.5 additions
* Track role `reference` (`music/model.py`): results of an earlier algorithm kept for comparison (muted,
  hidden, dashed); `music/melody.py` REFERENCES («Ref. v0.3 — …»), `references_of`, migration of v0.3/v0.4
  copies. Piano roll `compare_view`, track panel group «Referencias para comparar».
* `melody_worker.py` — `analyse_v03` (verbatim v0.3, written as `reference`), `transcription_lead` (the
  app passes the analysis's instrument notes in `notes`; `main_window._write_instrument_notes`).
  melody.json version 3. See `docs/MELODY_ANALYSIS.md`.
* `chordify_worker.py` — harmony refinement (`refine_chords`: key-tie prior, repetition pooling by lyric
  lines + DTW, no holes), commands `analyze` (now returns `chords` refined, `chords_raw`, `beats`,
  `harmony`) and `refine` («Revisar los acordes»: a new analysis, the old one untouched). See
  `docs/CHORDS.md`.
* `cifrado/guitar_builder.py` — lead-in and anticipation placement, no chord dropped;
  `GuitarCifrado.beats`; `playback/guitar.stroke_times` on the recording's beat grid.
* `cifrado/repeats.py` — repeated passages in tab cifrados; `TabSection.occurrences / repeat_of /
  originals` (serialised `occ` / `of` / `orig`, additive: older versions ignore them);
  `playback/tab_audio.performances` (display separate from playback); layout «se repite» blocks;
  `ui/repeats_dialog.py`. See `docs/REPEATS.md`.
* Guitar editor: chord-line buttons are given their parent before being shown (a parentless widget made
  visible becomes a top-level window: that was the flicker).
