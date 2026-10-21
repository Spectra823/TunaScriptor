# TunaScriptor

# Readme in different languages
[![en](https://img.shields.io/badge/readme-en-blue?style=flat)](https://github.com/Spectra823/TunaScriptor/blob/main/README.md)
[![es](https://img.shields.io/badge/readme-es-yellow?style=flat)](https://github.com/Spectra823/TunaScriptor/blob/main/README.es.md)
[![fr](https://img.shields.io/badge/readme-fr-red?style=flat)](https://github.com/Spectra823/TunaScriptor/blob/main/README.fr.md)

**From a recording to an editable *cifrado* for the instruments of a Spanish *tuna*.**

TunaScriptor (called *Tuna Cifrado Studio* in parts of the code and interface, name is not yet final and will be changed once development is completed) is a desktop application that
listens to a song and helps you write it down the way tuna musicians actually read music: string-and-fret
*cifrados* for bandurria and laúd, and lyrics-with-chords sheets for guitar. The analysis runs locally,
and everything it produces stays editable, because automatic transcription is a starting point, not
the final word.

> 🚧 **Work in progress.** TunaScriptor is under active development and is not yet ready to be installed or
> used on other computers. See [Development status](#development-status) below.

<p align="center">
  <img src="./mainwindowfoto.PNG" width="85%" alt="Analysis Main Window"/>
</p>

## Contents
- [Why TunaScriptor](#why-tunascriptor)
- [Development status](#development-status)
- [What it does today](#what-it-does-today)
- [How it works](#how-it-works)
- [Repository layout](#repository-layout)
- [Roadmap](#roadmap)
- [Documentation](#documentation)
- [Licence](#licence)

## Why TunaScriptor

In a *tuna* — the traditional Spanish student music ensemble — the bandurrias and laúdes play the melody and
its counter-voices, and the guitars accompany the singing. Most players don't read staff notation. They read
**cifrados**:

- for **bandurria and laúd**, a six-line tablature of fret numbers (one line per pair of strings), with the
  tuna's own conventions for tremolo, fast passages, pauses and repeats (BIS, ×N);
- for **guitar**, the lyrics with the chords written above the syllables where they change.

Writing these by ear from a recording takes a lot of time, and that is still how most of them are made.
TunaScriptor aims to do the first draft automatically:

- find the **main melody** (sung or instrumental) and the other parts of a recording;
- turn a part into a playable **bandurria or laúd cifrado**, choosing strings and frets the way a tuna
  player would;
- recognise the **lyrics and chords** of a song and lay them out as a **guitar cifrado**;
- notice **repeated sections** (estrofas, estribillos) so they are written once, not ten times;
- let you **listen, correct and print** the result.

It is meant for tuna members and arrangers, teachers preparing material for new members, and anyone who
wants a readable cifrado of a song without transcribing it note by note.

## Development status

TunaScriptor is a working prototype that is being improved step by step. Please read it that way:

- **Things will change.** The architecture, the analysis algorithms, the file formats and the interface
  are still evolving.
- **Results vary from song to song.** Recordings with a clear voice and few instruments give the best
  results. Dense arrangements, instrumental passages and unusual harmonies are where most errors appear.
- **The areas being worked on most** are the accuracy of the instrumental melody, chord transcription,
  recognition of repeated sections, and playback quality.
- **It is not ready for general installation.** It currently runs in a development setup on the author's
  machine. No installer has been published, and a clean installation on another computer has not been
  tested.
- **Setup and usage documentation for other computers** will be added once the application is ready for
  that.

Each change is measured before it is kept: the repository has an automated test suite, regression
scripts, and written records of what was measured and why (see [docs/STATUS.md](docs/STATUS.md) and
[CHANGELOG.md](CHANGELOG.md)).

## What it does today

Legend: ✅ works and is covered by automated tests · 🧪 works, but results are still unreliable or it has
not been fully tried with real recordings or hardware · 🔧 being actively reworked

### Analysis
| Feature | Status | Notes |
|---|---|---|
| Note transcription of a recording, one track per instrument | ✅ 🧪 | Uses the MuScriptor model; quality depends on the recording and the model size |
| Main melody (★): one line for the whole song, the voice where someone sings and the leading instrument elsewhere | 🔧 | Sung passages are tracked fairly reliably. Instrumental passages still produce spurious or missing notes |
| Vocal melody with optional voice separation | ✅ 🧪 | Vibrato stays one note; repeated syllables stay separate notes |
| Chord recognition (Chordify) and lyrics recognition (Whisper) | ✅ 🧪 | Chord accuracy is being validated against independent evidence; lyric recognition depends on the model |
| Comparison of TunaScriptor's revised chords with Chordify's original ones | ✅ | A developer script; disagreements are judged against the notes and the audio |
| Repeated sections (estrofa, estribillo, variations, BIS) | 🔧 | Works on many songs, but some are still split into too many fragments |

### Cifrados and editing
| Feature | Status | Notes |
|---|---|---|
| Bandurria and laúd cifrados from any transcribed part | ✅ | Tuna notation rules (tremolo, spacing, repeats, always six lines), tuna-style fingering, laúd written like bandurria |
| Guitar cifrados: lyrics with chords placed on the syllables | ✅ 🧪 | Spanish chord names (LAm, MI7…); placement follows the timing of words and chords |
| New cifrado by singing into the microphone (guitar, bandurria, laúd) | 🧪 | Recently added; tested with synthetic recordings, not yet with a real microphone |
| Transposition (by semitones, to a key, or into a vocal range) with an audible preview | ✅ | Strings and frets are re-chosen; notes out of range are flagged, never moved silently |
| Piano-roll note editing, cifrado editors, undo/redo | ✅ | Unsure notes and words are highlighted for review |
| Voices, soloists, colours and legend in guitar cifrados | ✅ | |

### Playback, saving and export
| Feature | Status | Notes |
|---|---|---|
| Playback of the recording, the notes or a selected fragment, with the score highlighted as it plays | ✅ 🧪 | Previews use simple synthesised sounds |
| Generated guitar accompaniment from a guitar cifrado, optionally with the separated voice | 🔧 | Synchronised by construction; the sound is still basic |
| Music library, saved analyses, independent cifrados, folders, autosave with crash recovery | ✅ | Original analyses are never overwritten; earlier melody results are kept |
| PDF export (with crest image and author) | ✅ | |
| Editable export and import: native format, MusicXML tablature, ChordPro, MIDI | ✅ | MusicXML has not yet been checked in MuseScore |


## How it works

The application is split into two kinds of processes, so that heavy machine-learning models never freeze
the editor or crash it:

```
┌──────────── Application (PySide6 / Qt) ─────────────┐
│ ui/        windows, piano roll, cifrado editors      │
│ cifrado/   cifrado models, fingering, chords,        │
│            repeats & structure, transposition        │
│ music/     notes, tracks, melody results, MIDI       │
│ playback/  audio player, synthesis, recording        │
│ library/   SQLite library, autosave, file storage    │
│ export/    PDF, MusicXML / ChordPro interchange      │
│ engines/   starts and supervises the engine workers  │
└──────────────┬───────────────────────────────────────┘
               │ JSON request on stdin → JSON lines on stdout
┌──────────────▼──────── Engine workers (separate Python environment) ──┐
│ muscriptor_worker.py  note transcription (MuScriptor, PyTorch)        │
│ chordify_worker.py    chords + lyrics (Chordify, Whisper)             │
│ melody_worker.py      main / vocal melody (librosa; optional voice    │
│                       separation with Mel-Band RoFormer)              │
└───────────────────────────────────────────────────────────────────────┘
```

- **Transcription** (bandurria and laúd workflow): MuScriptor transcribes the recording into instrument
  tracks. The melody worker adds a ★ main-melody track. It finds the predominant pitch with harmonic
  summation on a constant-Q spectrogram, tells the singing apart from the instruments, and follows the
  leading instrument outside sung passages. Any track can be converted into a cifrado, and the
  fingering engine chooses strings and frets.
- **Guitar workflow**: Chordify detects the chords and Whisper the lyrics. TunaScriptor reconciles
  repeated passages, removes only high-confidence junk text, and anchors each chord to the syllable
  where it changes.
- **Structure**: repeated sections are found by comparing the melody, the instrument tracks and the
  lyrics, so a repeated estribillo is written once. A time map keeps each repetition in sync with the
  recording for playback.
- **Storage**: a local SQLite library. Engine results are stored as immutable JSON files, and each
  cifrado is a self-contained JSON document that can be edited, duplicated or deleted independently.

More detail: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

**Main technologies:** Python, PySide6 (Qt 6), NumPy, SQLite, ReportLab (PDF), mido (MIDI). In the engine
environment: PyTorch, MuScriptor, Chordify, Whisper and librosa.

## Repository layout

```
src/tunacifrado/
  app/        start-up and the application context
  ui/         main window, library, piano roll, cifrado editors, dialogs
  cifrado/    bandurria/laúd and guitar cifrado models, fingering, chords, harmonisation,
              repeats and song structure, transposition, sung cifrados
  music/      note/track model, melody results and their versions, MIDI
  playback/   audio player, note and guitar synthesis, microphone recording, voice mix
  library/    SQLite database, autosave, file and analysis service
  export/     PDF and editable interchange formats
  engines/    engine environment, process runner and the standalone engine workers
  core/       paths, settings, logging, file helpers
tests/        automated tests (logic, offscreen GUI, and optional real-engine integration tests)
scripts/      developer tools: benchmarks, regression checks, chord comparison, screenshots
docs/         architecture, status, notation decisions, analysis notes, user guide (Spanish)
packaging/    early Windows packaging scripts (PyInstaller / Inno Setup), not yet in use
```

## Roadmap

| Area | Status |
|---|---|
| Main melody: fewer spurious and missing notes | 🔄 In progress: false notes during singing have been reduced, while notes that only the newer tracker finds are kept |
| Keep the reliable vocal tracking while improving instrumental passages | 🔄 In progress |
| Recognise repeated estrofas and instrumental sections without excessive fragmentation | 🔄 In progress: whole sections and variations are detected; some songs are still fragmented |
| Guitar chord accuracy and lyric alignment | 🔄 In progress: a repeatable comparison against Chordify and the audio is in place |
| Lyrics clean-up that removes only real video boilerplate | ✅ Done |
| Cifrados made by singing: accuracy with real microphone recordings | 🔍 Needs investigation: so far tested only with synthetic recordings |
| Playback quality and synchronisation | 🔄 In progress |
| UI reliability and fixing regressions | 🔄 Ongoing |
| Analysis speed, responsiveness and resource use | 📋 Planned |
| Testing, version control and documentation | 🔄 Ongoing: the automated test suite, tagged versions and changelog are in place |
| Installation and setup instructions for other computers | 📋 Planned, once the application is ready |


## Licence

The application code is released under the [MIT License](LICENSE).

The analysis engines and models are third-party software with their own terms, and they are not included
in this repository. In particular, the MuScriptor model weights are licensed **CC BY-NC 4.0** with gated
access conditions. See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md).
