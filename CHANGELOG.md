# Changelog

## 0.7.0 — 2026-10-19
Requested improvements of 2026-10-19 (details: docs/STATUS.md, docs/MELODY_ANALYSIS.md, docs/CHORDS.md,
docs/USER_GUIDE.md; versions and how to go back: VERSIONS.md).
- **Bandurria / laúd cifrado by singing** (＋ Nuevo cifrado… → Bandurria/Laúd cantando): record the melody by
  phrases (record, listen, redo, add parts, rename, reorder, clock) or add audio files; the voice is followed
  alone (melody worker «voice» mode: no separation); every note goes to a real, comfortable string/fret,
  sounding pitch preserved (laúd written an octave up, as usual); vibrato = one note, repeated syllables =
  separate notes; unsure notes shown **dudosas** (amber); out-of-range notes are never moved silently: «?»,
  and the report names the transpositions that would fit. «Altura»: as sung, or the octave that fits best.
- **Editor**: «▶ Grabación» (the recording with the notes highlighted), note menu (listen, mark sure/doubtful,
  split, merge), **«Transportar…»** (semitones, target key, or voice range — soprano … bajo — with preview of
  the transposed start and the new tonic; refingered; one undo step), **«🎙 Grabar otra parte…»** (append, or
  replace the selected section, without touching earlier edits).
- **Main melody refined with Vocal v0.3 as a reference** (melody results v5): held contours inside singing are
  kept only when v0.3 confirms them or they continue the voice. Note precision vs the user's «Alta precisión»
  voice: Si me quieres .652 → .705, Segadora .781 → .787, Agapimu .652 → .676, recall within .003; 116/120
  genuine notes found only by the new tracker kept.
- **Lyrics cleanup fixed** («Me pediste un beso» lost most of its lyrics): only segments that are entirely a
  known channel phrase are removed; loops and near-silent fragments are kept and underlined. Root cause:
  Whisper's compression ratio is per 30-s window, so a repeated chorus looked like a hallucination loop. Old
  analyses recover the lyrics when a new cifrado is created from them (Me pediste: 62 → 190 words).
- **Chord comparison TunaScriptor vs Chordify** (`cifrado/chord_compare.py`, `scripts/chord_compare.py`):
  normalised, aligned, disagreements judged against the notes and the audio; 0.89 exact agreement over 6 songs.
- Fix: closing a window while a preview was still rendering could start playback afterwards (and crash a
  headless test run); `Player.cancel()`.

## 0.6.0 — 2026-10-18
Requested improvements of 2026-10-18 (details and measurements: docs/STATUS.md, docs/REPEATS.md,
docs/MELODY_ANALYSIS.md, docs/CHORDS.md; versions and how to go back: VERSIONS.md).
- **Repeated passages written as whole sections** (estrofa, estribillo, interludes) instead of 4–7 s
  fragments: `cifrado/structure.py` finds the song's sections with the melody, every instrument of the
  analysis, the sung passages and the lyric lines (a repeated estribillo has the same words); limits on
  rests and lyric pauses; a different ending stays written out; a musical variation (harmony AND melody
  different for a sustained stretch) is labelled «A′ — variación de A»; different lyrics or a few wrong notes
  do not split an estrofa. Inside what is left only phrases played twice in a row become BIS, and only if they
  are the same notes. Part of the song written once, 7 of the user's songs (v0.5 → v0.6): Quiero 21 → 77 %,
  Campanera 0 → 68 %, Moliendo café 4 → 54 %, Las mañanitas 16 → 70 %, El Rey 5 → 99 %, Carmen 0 → 39 %, but
  Me pediste un beso 34 → 23 %; mean written-once part 4–10 s → 8–38 s; Quiero's limits agree with its lyric
  structure (recall 0.22 → 0.67, precision 0.25 → 0.86).
- **Every repetition sounds where it was played**: the notes written once are aligned to each occurrence's
  kept transcription (`cifrado/timemap.py`), so a slower second estrofa is no longer up to a second out of
  sync (synthetic rubato test: every note within 80 ms). «Usar como principal» and «Separar» use it too.
- **Melody**: octave coherence of the ★ melody (an instrument an octave up/down no longer wins single notes;
  confidently sung notes — vocal peaks — never move; no pitch ceiling). Campanera wrong-octave notes
  17 % → 11 %; the voice references unchanged or better. Results are versioned (`melody.v<N>.json` keeps
  every earlier result) and an outdated ★ melody is flagged with «★ Actualizar melodía principal».
- **Agapimu 0:00–0:10**: the PC still had v0.3 melody results (never re-run); the «Alta precisión» one has
  nothing before 10.5 s because it follows the separated voice only. The v0.5/v0.6 analysis fills the intro
  from the instruments (30 notes); the app now tells you the stored result is outdated.
- **«Quiero» chords**: Chordify's own chords are identical old/new; the v0.5 revision changes mainly F♯m → F♯.
  The recording's notes (MuScriptor) contain A♯ and no A in those bars, so the revision is kept as default,
  but «Crear un cifrado de guitarra» can now use Chordify's original chords, and chords on which the two
  versions disagree are marked «?».
- **«Revisar los acordes» removed** (the button and its worker command); the choice above replaces it.
- **Guitar playback**: real chord shapes (open chords, E/A-shape barres: Bm x24432…), one voice per string
  (a string struck again stops; at a chord change the fretted strings are lifted, open strings ring on),
  accents with the bass and lighter strokes without it, humanised timing (seeded), body resonance.
  **«🎸 Guitarra generada + 🎙 voz»**: the separated voice (kept from «Alta precisión» as `vocals.flac`) or,
  without it, the recording quieter with the voice band favoured — in sync by construction.
- **«＋ Nuevo cifrado…»**: guitarra, bandurria or laúd in blank, or **a guitar cifrado by singing**: record
  takes with the microphone (record, listen, redo, add parts, rename, reorder) or add audio files; the lyrics
  are recognised (unsure words underlined) or typed; the chords are **suggested** from the sung melody
  (`cifrado/harmonize.py`: key, tonal vocabulary, harmonic rhythm, repeated sections pooled) and marked as
  suggestions, with alternatives you can hear and pick from the chord menu; with guitar accompaniment the
  chords come from the audio. The recording is saved as a new song of the library.
- Safety: git with tagged versions v0.2.0–v0.6.0, data backup before changes, VERSIONS.md with restore steps.

## 0.5.1 — 2026-10-18
- **Fix: «No se encuentra el punto de entrada … torch_library_impl … _torchaudio.pyd»** during a
  transcription. Cause: the engine installer let uv pick torchaudio 2.11.0+cpu next to torch 2.9.0+cu129
  (torchaudio's releases stopped at 2.11; a torchaudio newer than torch cannot load). MuScriptor's tempo
  detection imports it. Now: «Instalar o reparar los motores» ends with a check that installs the
  torchaudio of torch's own version (torch is not touched, nothing large is downloaded); the engine
  workers turn off Windows' blocking DLL dialogs (a failure becomes a message); a mismatch is shown in
  «Motores…» and next to the model, and the tempo step is skipped instead of hanging on the dialog.

## 0.5.0 — 2026-10-18
Requested improvements (details and measurements: docs/STATUS.md, docs/MELODY_ANALYSIS.md, docs/CHORDS.md,
docs/REPEATS.md).
- **Original v0.3 melodies kept for comparison**: «Ref. v0.3 — Voz» and «Ref. v0.3 — Melodía principal»
  reference tracks (muted, hidden, dashed, own group) and «Comparar con v0.3». ★ Melodía principal stays the
  only primary melody; edited results are never overwritten.
- **Agapimu introduction**: the instrumental melody now follows the transcription's audible top line, checked
  against the recording with a local loudness reference, only away from the voice (intro: 20 → 30 notes,
  39 → 78 % covered, median step 9 → 2 semitones; `scripts/melody_regression.py`).
- **Repeated passages in bandurria/laúd cifrados** written once (BIS / Xn, «se repite»), every repetition
  still played in order at its own time, every original occurrence kept: inspect, play, choose as the main
  version, or write them out again («Repeticiones…»). Hand-written BIS / Xn are now played that many times.
- **Flicker fixed** when generating/opening guitar cifrados and changing the chord notation: a chord-line
  button was shown before having a parent and appeared as a separate window for an instant.
- **Guitar chords**: refined after Chordify (key ties, the audio decides major/minor dominants, repeated lyric
  lines pool their evidence when their harmony matches, no holes); the raw Chordify result is kept;
  «Revisar los acordes» for older analyses. Chords placed on the word the singer comes in on and on the next
  word when anticipated; no chord dropped. Generated guitar strums on the recording's beat grid.
- «Quiero» (Rosana) regression: F♯ over the first «Quiero», Bm on the third, the later «quiero, quiero y sigo
  queriendo» lines alike (`scripts/chord_regression.py`); the audio supports F♯ major, not F♯m.
- No new dependencies.

## 0.4.0 — 2026-10-18
Requested accuracy, playback, editing and UI improvements (details and measurements: docs/STATUS.md,
docs/MELODY_ANALYSIS.md).
- **Silent playback with many tracks (Agapimu) fixed.** Causes: the preview was normalised to its loudest
  instant, so with 16 tracks the mix came out at −42 dBFS (inaudible); Play pressed while the preview was
  still being generated was lost and the finished render then stopped the player. Now: loudness
  normalisation with a soft limiter (Agapimu: −42 → −24 dBFS RMS, render 7.2 → 0.5 s), Play / Fragmento
  pressed during the render start as soon as it is ready («Preparando el sonido…»). No notes or tracks
  are dropped.
- **One «★ Melodía principal» track** instead of two: the singer in the sung passages, the principal
  instrument in introductions and interludes; vibrato / sustained notes stay one note, repeated syllables
  and re-plucked notes stay separate. More of the instrumental passages are followed (e.g. Segadora
  28 % → 45 % of the instrumental frames). v0.3 projects are migrated without deleting anything.
- **Guitar editor: generated guitar accompaniment** (synthesised, clearly labelled) playing the cifrado's
  chords at their times, selectable strum pattern and tempo, follows edits; ♫ per line plays that line's
  chords.
- **Bandurria/laúd editor: add notes in empty positions** (click a blank spot on a string, type the fret;
  neighbours never move) and **per-section playback** (▶ beside each section title; correct laúd octave,
  tremolo, simultaneous notes).
- **Analysis view: several editable tracks at once** (✎ per track, ＋ target for new notes); box selection
  across editable tracks with left-drag, automatic scrolling at all four edges; **▶ Notas seleccionadas**
  (Ctrl+Space). A click never moves a note (4 px drag threshold).
- Guitar playback no longer blacks out the previous lyric line.
- PDF crest slightly larger (up to 40 × 28 mm), never overlapping the title.
- «Crear cifrado…» and «Exportar PDF…» scroll with many tracks; their buttons are always visible.
- No new dependencies.

## 0.3.0 — 2026-10-17
Requested improvements (details: docs/STATUS.md).
- **Lyrics**: routine video-channel phrases ("¡Suscríbete!", "Gracias por ver"…) are removed silently;
  other removals (repetition loops, no-voice segments) are reported with time and text. Lines that only
  contain similar words are kept.
- **Guitar playback** synchronised with lyrics and chords (line, word and chord highlighting, seek,
  Ctrl+click to jump). Word timing is stored per line and survives text edits; older cifrados are
  re-timed from their saved analysis.
- **Principal melodies**: new ★ tracks (sung notes, main melody) added beside the instrument tracks, with
  confidence per note; "only melodies" view; create a cifrado from selected/combined melody tracks with a
  confidence threshold or only a fragment. Analysis modes Rápido / Equilibrado / Alta precisión.
- **Fragment playback**: drag on the time ruler to select, adjustable edges, auto-scroll at the view edges,
  ▶ Fragmento stops exactly at the end.
- **Vocal detection**: melody worker (harmonic salience + contour shape) — on "A la fresca" it covers 63 % of
  the Large model's voice (Medium: 24 %) in ~4 s on CPU. Optional voice separation (RoFormer).
- **Bandurria/laúd**: texts beside section titles (independent of the notes), voice colours and legend
  (shared with guitar), shown in editor, text view and PDF.
- **Selection speed**: clicking a note in a 4,000-note cifrado went from ~300 ms to ~1 ms (only the visible
  strip is painted; only the two changed rectangles repaint; no per-note search while painting).
- **PDF**: optional crest image at the top right (persistent image library, last used preselected);
  author/nickname moved to the end.
- **Library**: cards view with cover art (from MP3 tags or chosen by the user); interface text size setting
  (5 presets, persistent, PDFs unaffected).
- **Editable export/import**: native `.tunacifrado`, MusicXML tablature (MuseScore etc.), ChordPro; import
  always creates a new cifrado and offers side-by-side comparison with the original.
- Library database schema v2 (images table, song cover column) — upgraded automatically.
- New requirement: `tinytag` (cover art; optional at runtime). Pillow is now listed explicitly.

## 0.2.1 — 2026-10-17
**Fixed: guitar analysis "La transcripción de la letra falló: FileNotFoundError: [WinError 2]".**
Cause: Whisper decodes audio by calling the `ffmpeg` program, which is not installed on most Windows
PCs. The Chordify worker now decodes the audio itself (libsndfile + soxr, resampled to 16 kHz mono)
and hands it to Whisper, so FFmpeg is no longer needed. Engine download errors are now classified
correctly (connection problems were reported as disk errors).

**Fixed: invented phrases such as "¡Suscríbete!" in the lyrics.** Whisper was trained on video subtitles
and sometimes "hears" channel boilerplate over instrumental passages. It is generated by the model —
the app only analyses the local file and never looks anything up online. The worker now drops such
segments (known video phrases, repetition loops, low-confidence no-speech segments) and tells the user
which phrases were removed.

## 0.2.0 — 2026-10-17
**Fixed: `run_dev.bat` closed without starting the app.** Cause (verified on the user's PC): `.venv`
was built from Anaconda's Python 3.14, where PySide6 fails with "DLL load failed while importing
QtCore"; the old launcher used `pythonw` through `start`, which hid the error.
- `setup_dev.bat` builds `.venv` with uv-managed standalone Python 3.12, rebuilds Anaconda-based
  environments, verifies imports and runs a UI self-test.
- `run_dev.bat` runs in the foreground with visible logs, pauses on errors, offers setup.
- New `diagnose_dev.bat`; `--self-test` option; logging starts before Qt is imported; clear message
  when Qt can't load; unhandled exceptions are logged and shown; engine environment never uses
  Anaconda either; `find_uv` also finds `uv` inside the dev `.venv`.
- Requirements use `PySide6-Essentials` + `PySide6-Addons` (≈130 MB less than the meta-package).

**Tuna notation implemented** (docs/NOTATION_DECISIONS.md): tremolo, spacing/rapid/arpeggio,
optional "—" pause, unambiguous multi-digit frets, "|" separators and same-staff segments,
BIS/Xn, always 6 rows, frets to 14, laúd = bandurria fingering, "Tuna" fingering rules, shared
layout engine for editor/text/PDF, strings drawn as lines in the PDF.

**Guitar**: explicit-M chord style by default, inline chord-only lines, unlimited voices/soloists
and combinations with colours, editable per-cifrado legend, dynamics, document colours.

Documents migrate automatically to format v2.

## 0.1.0 — 2026-10-16
First working version: library, both analysis routes (MuScriptor / Chordify) as managed local
processes, piano roll editing, string/fret and guitar cifrado editors, folders, autosave with crash
recovery, PDF/MIDI export, engine setup dialog, PyInstaller/Inno Setup recipes.
String/fret text layout is provisional pending the Tuna notation rules.
