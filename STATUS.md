# Project status — v0.7.0 (2026-10-19)

Legend: ✅ implemented and tested · 🟢 also verified on the user's Windows PC · 🟡 implemented,
not yet verified with real models/hardware · ⬜ not implemented yet

## Where it was tested
* **v0.5**: Linux sandbox (as below): 209 automated tests (7 skipped: they need the engine environment), plus
  with the engine environment 17 engine-side tests (melody, chords) and 3 integration tests against a real
  engine; `scripts/melody_regression.py` (Agapimu) and `scripts/chord_regression.py` (Quiero) PASS on the
  user's recordings. Copied to the user's PC, not started there by me. **Nothing was listened to.**
* **User's Windows 10 PC** (via the linked desktop): `setup_dev.bat` / `run_dev.bat` (v0.1–v0.3). v0.4 adds
  no dependency; it was copied to the PC but not started there by me.
* Linux sandbox, Python 3.13, PySide6 6.12, offscreen Qt: 189 automated tests (4 skipped) + 3 integration
  tests against a real engine environment. **No audio device here: nothing was listened to.** Audio
  correctness is checked on the rendered samples (levels, onsets, pitches), not by ear.
* The user's own library (copy): Agapimu, Si me quieres escribir, Segadora, Campanera, A la fresca,
  Cu-cu-rru-cu-cu paloma (audio + saved analyses).
* Hugging Face and OpenAI downloads are blocked here: MuScriptor, Whisper and the RoFormer voice separation
  were not run in v0.4; the melody comparison uses the user's own «Alta precisión» and Large results.

## v0.7.0 — requested improvements (2026-10-19)
Tested here: 293 automated tests pass with the engine environment (4 skipped: Windows-only / model downloads),
including 2 new integration tests that run the REAL melody worker on a synthetic sung phrase and build a
bandurria and a laúd cifrado. **Nothing was listened to; no microphone and no Whisper weights here.**

| # | Request | State | How it was verified |
|---|---|---|---|
| §9 | Bandurria/laúd cifrado from singing: blank, record by phrases, transcription, real positions, Transportar (key/voice, audible preview), review/edit (dudosas, split/merge, recording playback, record another part), save as new | ✅ 🟡 | 8 unit tests (positions, doubtful, out-of-range kept + fitting transpositions named, takes → sections, transpose/undo, PDF export), 3 GUI tests (fake microphone: create → edit → save/reopen; Transportar; append/replace part), 2 real-worker tests: vibrato, 3 repeated syllables, rest, leaps, run, noise → 12/12 notes, exact pitches, valid positions on both instruments. **Microphone not tested here** (no device); denial/failure paths tested with a fake recorder |
| B | Main melody refined with Vocal v0.3 | ✅ 🟡 | 3 songs vs the user's «Alta precisión» voice: note precision +0.6…+5.3 points, recall −0.3 points at most, 116/120 genuine new notes kept; Campanera F .620 → .622; synthetic benchmark (docs/MELODY_ANALYSIS.md). **Needs «★ Actualizar melodía principal» on the PC** |
| C1 | Chords TunaScriptor vs Chordify | ✅ | 6 songs, 1009 s: 0.89 exact agreement; the only large disagreement (Quiero F♯/F♯m, 34 s) leans TunaScriptor (docs/CHORDS.md). Agreement, not accuracy: no verified chords exist |
| C2 | Lyrics cleanup («Me pediste un beso») | ✅ | root cause fixed; regression tests (repeated choruses, boilerplate before/after, mixed lines, ambiguous phrases, short songs, share removed); real analysis 62 → 190 words, only 2 channel phrases removed. Existing cifrados are not changed: create a new one from the analysis |

## v0.6.0 — requested improvements (2026-10-18 evening)
Tested here: 255 automated tests pass (with the engine environment; 4 skipped: Windows-only / model downloads),
including 1 new integration test that runs the REAL melody worker on a synthetic sung recording. Benchmarks
on copies of the user's recordings and analyses. **Nothing was listened to; no microphone here.**

| # | Request | State | How it was verified |
|---|---|---|---|
| 1 | Repetitions as whole sections (estrofa, estribillo), variations kept, timing per occurrence | ✅ 🟡 | 7 real songs: written-once 0–34 % → 23–99 % (Me pediste: 34 → 23 %), limits vs lyric structure (Quiero R/P 0.67/0.86); synthetic songs exact; playback onset integrity 0.88–0.99 (docs/REPEATS.md) |
| 2 | Main melody: octave errors / accompaniment notes / vocal peaks | ✅ 🟡 | Campanera GT (user's PDF): wrong octave 17 → 11 %, F unchanged; voice refs unchanged or better; Agapimu instrumental plausibility 0.92 → 0.86 (docs/MELODY_ANALYSIS.md) |
| 3 | «Quiero» guitar chords | ✅ (kept + choice) | raw Chordify identical; revision F♯m→F♯ supported by the recording's notes (A♯, no A); raw chords selectable, disagreements «?» (docs/CHORDS.md) |
| 4 | Agapimu 0:00–0:10 | ✅ 🟡 | PC results are v0.3; v0.6 analysis: 30 notes before 10 s; outdated results flagged. **Needs a re-run on the PC** |
| 5 | «Revisar acordes» button | ✅ removed | button + worker command removed; GUI test |
| 6 | Guitar playback naturalness + synchronized voice | ✅ 🟡 | chord shapes, per-string voices, humanised strums (unit tests); voice mix (unit + GUI tests). **Not heard** |
| 7 | §10 sing → guitar cifrado; blank cifrados | ✅ 🟡 | 13 unit tests, 4 GUI tests (fake microphone, prepared engine results), real melody worker on a synthetic sung C–F–G7–C → C F G7 C; chords from the voice vs Chordify proxy: 0.62 mean (tonic baseline 0.38). **Lyrics recognition and the microphone were not run here** (Whisper download blocked, no audio device) |
| 8 | Version control and recoverable versions | ✅ | git tags v0.2.0–v0.6.0, VERSIONS.md, archives restored and their tests run |

## v0.5.0 — requested improvements (2026-10-18)
| # | Feature | State | How it was verified |
|---|---|---|---|
| 1 | Original v0.3 melodies kept as reference tracks, «Comparar con v0.3» | ✅ | v0.3 algorithm reproduced note for note on 6 songs (this machine); migration tests (v0.3 project, v0.4-saved copies, edited copies never overwritten); GUI test of the compare view. PC re-runs can differ by a few notes (MP3 decoding) |
| 2 | Agapimu instrumental introduction | ✅ | cause measured (quiet intro vs song loudness; partials); `scripts/melody_regression.py` PASS: 30 notes, 78 % covered, median step 2 (v0.4: 20 / 39 % / 9); voice agreement unchanged on 3 songs; unit tests. **Not heard** |
| 3 | Repeated passages in bandurria/laúd cifrados (BIS / Xn / «se repite») | ✅ | 10 tests (detection, playback of every repetition in order and time, consensus recovery, variations kept, recovery/choose main/separate, MusicXML, PDF, speed) + GUI test (dialog, propagation message, undo). On the user's library: 0.00–0.11 s per track (docs/REPEATS.md). Musical correctness of the passages found **not checked by ear** |
| 4 | Flicker opening/generating guitar cifrados and changing notation | ✅ | root cause found with an event filter (5 stray windows per open/notation change); regression test fails on the old code |
| 5 | Chord accuracy, lyric alignment, natural guitar playback | ✅ 🟡 | engine tests on synthetic chroma; builder tests (lead-in, anticipation, no chord lost); Quiero: reference vocabulary 0.49 → 0.70, holes 6.4 → 1.3 s, strokes on the beat grid 24 → 100 %, double strokes 24 → 0 (docs/CHORDS.md). **Not heard** |
| 6 | Repetition-aware chord reconciliation | ✅ | pooling test (a masked bar recovered, a real difference kept); repeat consistency 0.55 → 0.88 (Quiero), 0.60 → 0.92 (Maldita Dulzura) |
| 0.5.1 | torchaudio DLL error dialog on the PC («punto de entrada torch_library_impl») | ✅ 🟡 | cause read from the PC's engine environment (torch 2.9.0+cu129, torchaudio 2.11.0+cpu, installed by the MuScriptor step); repair step + dialog suppression + warnings; 6 tests (rule, repair with a simulated broken env, never fails the install, plan order, workers, real env unchanged). **The repair itself has not run on the PC yet** (PyTorch's index is not reachable from here) |
| 7 | External chord references, «Quiero» | ✅ | three chord sites checked against the recording (same song, key, no capo); `scripts/chord_regression.py` PASS. F♯ (not F♯m) is what the audio supports |

## v0.4.0 — requested improvements (2026-10-18)
| # | Feature | State | How it was verified |
|---|---|---|---|
| 9 | Silent playback with many tracks | ✅ 🟡 | Root causes: peak normalisation (16-track Agapimu at −42 dBFS) + Play lost while the preview rendered. Real Agapimu (10,718 notes, 18 tracks): −24 dBFS, 0.14 % samples > 0.97, render 0.6 s (was 7.2 s). Tests: single track, several tracks, 9,600-note arrangement, lone quiet note, Play/Fragmento during render, stop/restart, re-render while playing. **Not heard** |
| 1 | One «★ Melodía principal» (voice / instrumental lead, vibrato & repeated notes) | ✅ | Before/after on synthetic audio with known notes and on 3 real songs vs the user's separated-voice and Large references (docs/MELODY_ANALYSIS.md); 9 unit tests; real-engine test; migration on a copy of the user's library (5 analyses: ★ count 2 → 1, edits kept, nothing written on open). Agapimu instrumental plausibility went down (77 → 69 %) |
| 2 | Generated guitar accompaniment from the cifrado | ✅ 🟡 | strums start at every chord time (onset energy), edits/patterns change the audio, line playback, single instance; GUI test with a fake media backend. **Not heard** |
| 12 | ♫ chord-line playback in the guitar editor | ✅ 🟡 | same; line without chords hidden / message |
| 3 | Previous lyric line no longer blacked out | ✅ | window-pixel test (fails on the old code) |
| 4 | Add notes in empty positions (bandurria/laúd) | ✅ | GUI test: gap, simultaneous, two-digit fret, neighbours' positions unchanged, undo, PDF/text |
| 11 | Per-section playback (bandurria/laúd) | ✅ 🟡 | sounding pitches (laúd −12), simultaneous, tremolo strokes, «?» skipped, empty section message, re-render on edit, layout unchanged; **not heard** |
| 5 | Several editable tracks at once | ✅ | GUI test: edit/target states separate from M/S/Ver; analysis document unchanged |
| 6 | Left-drag box selection across tracks, ▶ Notas seleccionadas | ✅ 🟡 | GUI test (single/multiple tracks, overlaps, empty selection, click doesn't move). Partially covered notes are selected and played whole |
| 7 | Vertical autoscroll while selecting | ✅ | GUI test |
| 8 | Larger PDF crest without overlap | ✅ | tests: 3 image shapes, single and multi-page |
| 10 | Scrollable «Crear cifrado…» / «Exportar PDF…» | ✅ | GUI test with 2 and 60 tracks: buttons inside the screen |

## v0.3.0 — requested improvements (2026-10-17)
| # | Feature | State | How it was verified |
|---|---|---|---|
| 1 | Silent removal of video-channel phrases; other removals reported | ✅ | unit tests (phrases ES/EN, real lyrics with similar words kept) |
| 2 | Guitar playback with line/word/chord highlighting, seek, Ctrl+click | ✅ 🟡 | sync logic + editor highlighting tested offscreen with simulated time; **audio itself not heard here** (no audio device) |
| 3.2 | ★ melody tracks beside instrument tracks, only-melodies view, cifrado from selected/combined melody tracks, confidence threshold | ✅ | unit + GUI tests; real "A la fresca" analysis loaded with melody tracks |
| 3.3 | Fragment selection + playback | ✅ 🟡 | selection/edges/Esc tested; exact stop uses a 30 ms timer — **not heard here** |
| 3.4 | Auto-scroll while dragging at the edges | ✅ | GUI test drags to the edge: view scrolls, region keeps growing past the visible area |
| 4 | Vocal melody detection, modes Rápido/Equilibrado/Alta precisión | ✅ 🟡 | real "A la fresca": voice coverage 24 % → 63 % vs Large, pitch 57 % → 80 %, 4 s CPU (docs/MELODY_ANALYSIS.md); synthetic ground-truth benchmark; real-engine integration test. **Alta precisión (RoFormer) not run**: weights download blocked here |
| 5 | Texts beside section titles, colours, legend (bandurria/laúd) | ✅ | tests prove notes don't move (layout and PDF glyph positions identical) |
| 6 | Faster note selection | ✅ | `scripts/bench_tab_editor.py`: 4,000 notes, click→repaint 296 ms → 1.2 ms (median); edit 356 → 50 ms |
| 7 | Crest image in PDF + persistent image library | ✅ | tests: persistence across reopen, last-used default, aspect ratio, no overlap with title |
| 8 | Author at the end | ✅ | PDF/text tests |
| 9 | Library cards with cover art; text size setting | ✅ | GUI test with a real MP3 + ID3 cover; screenshots at 100 % and 150 % |
| 10 | Editable export/import (native, MusicXML tab, ChordPro) | ✅ | XSD-valid MusicXML 4.0, music21 reads it, round trips; user's real 5,838-note cifrado identical after export+import. **Not opened in MuseScore here** |

Sandbox test run: 151 passed, 4 skipped (+ 3 real-engine integration tests passed separately).

## Development environment
- 🟢 `setup_dev.bat`: uv-managed Python 3.12, rebuilds Anaconda-based `.venv`, UI self-test
- 🟢 `run_dev.bat`: foreground, visible logs, error pause, offers setup when needed
- ✅ `diagnose_dev.bat`: read-only diagnostic report
- ✅ Startup logs before Qt loads; clear message if Qt can't load; unhandled errors logged + shown

## Notation (all confirmed rules — see NOTATION_DECISIONS.md)
- ✅ Tremolo ´ (structured), detection on conversion
- ✅ Spacing model: rapid / arpeggio (0), normal, longer pauses; optional explicit "—" pause
- ✅ Unambiguous multi-digit frets (short dash only where needed)
- ✅ "|" separators, same-staff continuation segments, BIS / Xn repeat counts
- ✅ Always 6 rows; frets up to 14 (12 regular)
- ✅ Laúd = bandurria fingering (written pitch); whole-part octave auto/manual
- ✅ "Tuna" fingering strategy (5th-fret rule on strings 2–6, 1st-string exception, whole passage)
- ✅ Author/nickname field
- ✅ One layout engine for editor grid, text view and PDF
- ✅ Guitar: explicit-M chord style default (LAm, DOM, LAM…), maj7 unambiguous
- ✅ Guitar: chord-only lines inline ("Introducción: LAm MI7 …") in PDF and editor
- ✅ Guitar: unlimited voices/soloists, combinations, per-cifrado colours, editable legend
  (add/remove/rename/reorder/recolour, position, title), safe removal with reassignment,
  printed tags for grayscale, dynamics per line/section, paper-white editing sheet
- ✅ v0.1 documents migrate automatically (format v2)

## Earlier milestones (unchanged since v0.1 unless noted)
- ✅ Library window, import, duplicates, favourites, missing files, safe cleanup
- ✅ Autosave with crash recovery; undo/redo in all editors
- ✅ Piano roll editing, tracks (rename/hide/mute/solo), MIDI import/export
- ✅ Multiple independent cifrados, optional folders, side-by-side editors, PDF export
- ✅ Chordify route on a local file through the GUI (real engine, synthetic audio)
- 🟡 MuScriptor transcription success path (needs weights), Whisper lyrics
- 🟡 Playback (no audio device in the sandbox; not yet tried on the PC)
- 🟡 Windows installer (PyInstaller spec built on Linux; Inno Setup script untested)

## Not implemented yet
- ⬜ Dynamics on bandurria/laúd cifrados (voices/colours/legend done in v0.3)
- ⬜ Synchronised playback cursor in the bandurria/laúd editor (guitar done in v0.3)
- ⬜ MuScriptor pass on the separated vocal stem (investigated, see MELODY_ANALYSIS.md)
- ⬜ Chord diagrams in guitar PDFs; manual tempo/bar editing
- ⬜ Real bandurria/laúd/guitar samples for preview (synthesised sounds only)
- ⬜ Signed installer, app icon, update checker
