# Third-party components and licences

Verified on 2026-10-16 from package metadata, repository LICENSE files and the
HuggingFace model page. Re-check before each release — terms can change.

The application's own code is MIT (see `LICENSE`). That licence does **not**
override anything below.

## 1. Bundled with the Windows application (GUI)

| Component | Licence | Notes |
|---|---|---|
| Python runtime | PSF License | via PyInstaller |
| PySide6 / Qt 6 | LGPL-3.0 (also GPL) | Shipped as separate shared libraries (PyInstaller "onedir"), so users can replace them, as LGPL requires. Qt Multimedia's FFmpeg backend is LGPL-2.1+. |
| NumPy | BSD-3-Clause (+ 0BSD, MIT, Zlib, CC0 parts) | |
| soundfile + libsndfile | BSD-3-Clause / LGPL-2.1+ | libsndfile shipped as a separate DLL |
| mido | MIT | MIDI read/write |
| ReportLab | BSD | PDF export |
| psutil | BSD-3-Clause | memory detection |
| uv (optional, `tools/uv.exe`) | MIT OR Apache-2.0 | installs the engine environment on first run |

## 2. Downloaded on first run, into a separate engine environment (not bundled)

| Component | Licence | Notes |
|---|---|---|
| MuScriptor 0.3.0 (code) | MIT | Kyutai × Mirelo. https://github.com/muscriptor/muscriptor |
| Chordify @ `bdc97f7` (code) | MIT | Lucas Maciel. https://github.com/1ucas/chordify — installed **without** its yt-dlp dependency; its YouTube downloader is never used. Status: alpha. |
| PyTorch / torchaudio | BSD-style / Apache-2.0 (+ LLVM exception) | CPU or CUDA build chosen automatically |
| openai-whisper (code **and** weights) | MIT | lyric recognition |
| librosa | ISC | Chordify's chord detector |
| SciPy, numba | BSD | |
| huggingface_hub, safetensors | Apache-2.0 | |
| beat-this (code) | MIT | MuScriptor's tempo detector. **Its checkpoint is downloaded from JKU's server on first use; the checkpoint's licence is not stated in the package and was not verified.** Tempo detection is optional; transcription works without it. |
| soxr | LGPL-2.1+ | |
| typer, rich, einops, tiktoken, fastapi | MIT | |

## 3. Model weights (downloaded only after the user confirms; never bundled)

### MuScriptor (small / medium / large) — HuggingFace `MuScriptor/muscriptor-*`
* Licence: **CC BY-NC 4.0** (non-commercial), **plus gated access conditions** that each user accepts on the model page with a free HuggingFace account.
* The access conditions (summarised — read the original on the model page) require that the user **holds all necessary rights, including intellectual-property rights, over the music they input and the resulting output**; they list transcribing music files to MIDI/sheet music without such rights as a prohibited use; they include an **indemnity** towards Mirelo and Kyutai; the model is provided without warranty.
* Consequence for this project: the weights are **not** redistributed and the gate is **not** bypassed (even though CC BY-NC alone might allow non-commercial sharing, bundling would skip the authors' access conditions). The app guides each user through accepting them and stores the user's token only through `huggingface_hub`'s own mechanism (never in this app's files or logs).
* Attribution: Rouard, Krause, Roebel, Simon-Gabriel, Défossez, *MuScriptor: An Open Model for Multi-Instrument Music Transcription*, arXiv:2607.08168 (2026).

### Whisper (small / medium / turbo / large-v3)
* Licence: MIT (OpenAI). Downloaded from OpenAI's servers by `openai-whisper` after the user confirms.

### Optional, not installed by default
* Mel-Band RoFormer vocal model (Kimberley Jensen, MIT) and Demucs `htdemucs_ft` (MIT): only used by Chordify's optional source separation, which this version does not enable.

## 4. External tools the app can use if present
* FFmpeg (LGPL/GPL depending on build): only for M4A/MP4/AAC and similar formats. Not bundled in this version. If a future release bundles it, use an LGPL build and ship its licence and source offer.

## 5. Recordings
The app ships no copyrighted recordings. Test fixtures are synthesised by the test-suite itself.
