# Versions and how to go back

TunaScriptor (Tuna Cifrado Studio) is kept in git. **Stable releases** are tagged on branch `main`;
work in progress lives on `dev-<version>` branches and is merged to `main` only when its tests pass.
At all times at least one verified, recoverable version of the best-known working state exists.

| Tag | Date | What | Verified |
|---|---|---|---|
| v0.2.0 | 2026-10-17 | guitar & bandurria/laúd cifrados, library, PDF | reconstructed from the synced copies |
| v0.2.1 | 2026-10-17 | fixes after v0.2 | reconstructed |
| v0.3.0 | 2026-10-17 | melody detection, guitar playback, voices/legend | reconstructed |
| v0.4.0 | 2026-10-18 | audible previews, editing/playback improvements | reconstructed; its suite: 189 passed / 4 skipped |
| v0.5.0 | 2026-10-18 | reference tracks, Agapimu intro, repetitions v1, chord revision | reconstructed |
| v0.5.1 | 2026-10-18 | torchaudio DLL fix | restored from its zip: 215 passed / 7 skipped |
| v0.6.0 | 2026-10-18 | sections, melody octaves, Quiero chords, guitar + voice, sing → cifrado | 255 passed / 4 skipped (with engines); restored from its zip: 251 passed / 8 skipped (no engines) |
| v0.7.0 | 2026-10-19 | bandurria/laúd by singing, Transportar, melody refined with v0.3, lyrics cleanup fix, chord comparison | 293 passed / 4 skipped (with engines); restored from its zip: 287 passed / 10 skipped (no engines) |

v0.2.0–v0.5.1 were reconstructed from the exact copies that were synced to the PC at each release (the staged
sync folders); the reconstructed v0.5.1 tree is identical to what is installed on the PC.

## Downloadable copies
`TunaScriptor-versions/` (next to the project on the PC, and sent in the conversation):
* `TunaScriptor-vX.Y.Z.zip` — the source of each tagged version (`git archive`), ready to use;
* `TunaScriptor-all-versions.bundle` — the whole git history in one file (`git clone TunaScriptor-all-versions.bundle`);
* `SHA256SUMS.txt` — checksums.

## Restore a version
**Program** (your songs, analyses and cifrados are NOT inside the program folder and are not touched):
1. Close TunaScriptor.
2. Rename the current program folder (e.g. `TunaCifradoStudio` → `TunaCifradoStudio-0.7.0`), do not delete it.
3. Unzip `TunaScriptor-vX.Y.Z.zip` into a folder named `TunaCifradoStudio` in the same place.
4. Start it with `run_dev.bat` as usual (the engine environment is shared; `setup_dev.bat` only if asked).
With git instead: `git checkout vX.Y.Z` (or `git switch -c try-old vX.Y.Z`).

**Data** (library, analyses, cifrados): kept in `%LOCALAPPDATA%\TunaCifradoStudio`. Newer versions read older
data; nothing is migrated destructively (melody results keep every earlier version as `melody.v<N>.json`;
analyses are immutable; cifrados are only changed when you edit them). A full copy of the data taken before the
v0.6 changes is kept (`data-2026-10-18-before-0.6`, with SHA256SUMS). To go back to it: close the app, rename
`%LOCALAPPDATA%\TunaCifradoStudio` and copy the backup in its place.

## Rules followed for every change
* A checkpoint (commit) before every risky change; experiments on the dev branch only.
* Analysis outputs are never overwritten: new analyses are new records; earlier melody results are kept.
* Each release: tests, CHANGELOG entry, tag, archives, restore check (unzip + run its tests).

The archives were checked by unzipping v0.5.1, v0.6.0 and v0.7.0 into empty folders and running their own test suites,
and by cloning the bundle (all 8 tags present).
