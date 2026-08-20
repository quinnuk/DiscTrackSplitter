# 🎧 DiscTrackSplitter

<p align="center">
  <strong>Split ripped Blu-ray concert & music discs into chapter-named song files, preserving your choice of audio track without re-encoding.</strong>
</p>

<p align="center">
  A lightweight Windows utility that safely extracts and splits Blu-ray concert and music tracks without quality loss.
</p>

<p align="center">
  <a href="https://github.com/quinnuk/DiscTrackSplitter/releases/latest">
    <img src="https://img.shields.io/github/v/release/quinnuk/DiscTrackSplitter?style=for-the-badge" alt="Latest Release">
  </a>
  <img src="https://img.shields.io/badge/platform-Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white" alt="Windows">
  <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white" alt="Python 3.10+">
  <a href="https://github.com/quinnuk/DiscTrackSplitter/blob/main/LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-green?style=for-the-badge" alt="License">
  </a>
</p>

<p align="center">
  <a href="https://buymeacoffee.com/quinnuk" target="_blank">
    <img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="60" width="217">
  </a>
  <br>
  <sub>If DiscTrackSplitter saves you time organizing your concerts, a coffee is always appreciated ☕</sub>
</p>

<p align="center">
  <img src="screenshot.png" alt="DiscTrackSplitter application screenshot" width="900">
</p>

---

## 📑 Contents

- [Is This For You?](#-is-this-for-you)
- [Why This Exists](#-why-this-exists)
- [Key Features](#-key-features)
- [How It Works](#️-how-it-works)
- [Getting Started](#-getting-started)
- [Installing the External Tools](#-installing-the-external-tools)
- [Building a Standalone .exe](#-building-a-standalone-exe)
- [Recovering from an Interrupted Run](#-recovering-from-an-interrupted-run)
- [Project Layout](#-project-layout)
- [Notes & Known Limitations](#-notes--known-limitations)
- [Reporting Issues](#-reporting-issues)
- [Support This Project](#-support-this-project)
- [License](#-license)

---

## ❓ Is This For You?

You've ripped a Blu-ray Audio disc — a concert film, a live album, or one of the growing number of immersive-audio reissue Blu-rays — and you want it in your **Plex / Jellyfin / Kodi** library as individual, correctly-named song files instead of one giant feature-length file. If that's you, this tool exists for exactly that job, and nothing else.

It is **not** a ripping tool, a re-encoder, or a general-purpose media converter — see [Notes & Known Limitations](#-notes--known-limitations) for what it deliberately doesn't do.

> **Sibling Project:** This is a sibling project to [AtmosTrackSplitter](https://github.com/quinnuk/AtmosTrackSplitter). While that tool only ever extracts the Dolby Atmos track, **DiscTrackSplitter** lists *every* audio track a playlist actually has — Atmos, TrueHD, DTS-HD Master Audio, LPCM, in whatever channel layouts (2.0 / 5.1 / 7.1) and sample rates the disc offers — and lets you pick.

---

## 💡 Why This Exists

Concert Blu-rays and hi-res reissue discs are usually one giant file with chapter markers per song, and often carry two or more versions of the mix — a stereo LPCM master alongside a 5.1 DTS-HD MA mix, for example.

Most existing tools either:
- **Flatten or transcode** the audio (losing the format you actually wanted), or
- Give you the **whole disc as one file** with no easy way to split it or pick a track, or
- Require you to **hand-build complex `mkvmerge` / `ffmpeg` commands** per chapter.

**DiscTrackSplitter** automates the whole process: point it at the ripped disc folder, pick which audio track you want to keep, name the tracks (typed, pasted, imported, or looked up via text/barcode), and get one clean, bit-exact file per song.

---

## ✨ Key Features

| | Feature | Description |
|---|---|---|
| 🔍 | **Auto-detects Playlist** | Scans every `.mpls` playlist, scores candidates (Atmos presence or best available lossless track, chapter count, duration), and displays its reasoning. |
| 🎚️ | **Audio Track Picker** | Displays codec, channels, sample rate, bitrate, and bit depth. Pre-selects Dolby Atmos or best lossless mix automatically. |
| 🏷️ | **Barcode & Text Lookup** | Query **MusicBrainz** directly using the disc's UPC/EAN barcode or release title, with per-track match confidence shown so you can review before committing. |
| 📝 | **4 Track Naming Methods** | Paste plain lists, import files (`.txt`, `.nfo`, `.cue`, `.json`), pull embedded disc chapter names, or search MusicBrainz via text or barcode. |
| 📂 | **Sidecar Tracklist Detection** | Automatically flags matching tracklists already sitting in your disc rip folder. |
| ⏸️ | **Resumable Jobs** | Progress is checkpointed to a manifest so mid-job crashes or cancels won't lose your progress. |
| 📋 | **Per-Job Logging** | Every run writes an `extraction.log` accessible with a single click. |
| ✂️ | **Zero Re-encoding** | Stream-copies video and chosen audio only — bit-exact audio quality with TV video playback retained. |
| 🪟 | **Standalone Executable** | Can be built as a clean, windowed `.exe` with no attached console. |

---

## ⚙️ How It Works

1. **Point** the app at a folder containing a ripped disc (must have standard, unmodified `BDMV/PLAYLIST/*.mpls` structure).
2. **Inspects** every playlist via `mkvmerge -J` (JSON output, chosen for stability across MKVToolNix versions), scoring candidates for the main feature and showing its reasoning.
3. **Lists** all audio tracks, pre-selecting Atmos or the best available lossless stream. Choose a different track anytime!
4. **Name** chapters using paste, import, embedded names, or **MusicBrainz** lookup (via album name or **barcode scanner/UPC entry**).
5. **Extracts** video + chosen audio via `mkvmerge` (stream-copy, no transcoding) and splits into individual chapter files using `ffmpeg`.

---

## 🚀 Getting Started

### 📦 Option 1 — Pre-compiled EXE

**[⬇️ Download the latest release](https://github.com/quinnuk/DiscTrackSplitter/releases/latest)**

1. Download `DiscTrackSplitter.exe`.
2. Run it — **no Python installation is required**.
3. Install the required external tools listed below if they are not on your system `PATH`.

---

### 🐍 Option 2 — Run from Source

Install the required Python packages:

```bash
pip install -r requirements.txt
```

Then run the app:

```bash
python main.py
```

---

## 🔧 Installing the External Tools

DiscTrackSplitter doesn't bundle mkvmerge, mkvextract, ffmpeg, or ffprobe — it shells out to them. Either of these works:

- **Put them on your system `PATH`.** Standard installs of [MKVToolNix](https://mkvtoolnix.download/downloads.html) and [ffmpeg](https://ffmpeg.org/download.html) do this automatically on Windows.
- **Point the app at them directly.** If a tool isn't found on launch, the app shows a row for it with **Download** and **Browse...** buttons — Browse lets you pick the `.exe` by hand without touching your system `PATH`. Picking one half of an MKVToolNix install (e.g. `mkvmerge.exe`) also auto-fills the other (`mkvextract.exe`) if it's sitting alongside it.

Paths you set this way are remembered in `%USERPROFILE%\.disc_track_splitter\settings.json`, so this is a one-time setup.

---

## 🏗️ Building a Standalone .exe

From a source checkout with `requirements.txt` installed:

```bash
build_exe.bat
```

This runs PyInstaller (`python -m PyInstaller --onefile --windowed`) and produces `dist\DiscTrackSplitter.exe` — a single windowed executable with no attached console, using `disc_track_splitter.ico` as its icon.

Note that this bundles the Python app itself only — mkvmerge, mkvextract, ffmpeg, and ffprobe are **not** included and still need to be installed separately (see above), same as with the pre-compiled release.

---

## ⏸️ Recovering from an Interrupted Run

Every job writes a `manifest.json` into its work folder as it progresses (audio extracted, which chapters have been split so far). If the app crashes, is closed, or a job is cancelled mid-run, that manifest is left in place rather than cleaned up.

Point the app at the same source/output pair again and it will detect the existing manifest and offer to **resume** — skipping the already-extracted audio and already-split chapters instead of starting over. This also covers retrying just the chapters that failed on a previous attempt.

If you only need to re-split an already-extracted audio file (skipping the GUI and disc scan entirely), `split_now.py` does that directly from the command line:

```bash
python split_now.py EXTRACTED_MKV OUTPUT_FOLDER --names-file tracks.txt
```

Pass `--list-only` instead of `--names-file` to just see the chapters in a file without splitting anything yet.

---

## 📁 Project Layout

| File | Purpose |
|---|---|
| `main.py` | GUI entry point — the CustomTkinter application itself. |
| `extractor.py` | Core logic: disc scanning, playlist scoring, track extraction, chapter splitting, MusicBrainz lookups. No GUI dependencies — usable standalone. |
| `settings.py` | Small JSON-backed store for tool paths and last-used folders. |
| `split_now.py` | CLI tool for splitting an already-extracted audio file without going through the GUI. |
| `requirements.txt` | Python dependencies. |
| `DiscTrackSplitter.spec` / `build_exe.bat` | PyInstaller build configuration and build script for the standalone `.exe`. |
| `disc_track_splitter.ico` | Application icon, used by both the app window and the built `.exe`. |

---

## ⚠️ Notes & Known Limitations

- **Not a ripping tool.** DiscTrackSplitter works on an already-ripped, unencrypted disc folder with a standard `BDMV/PLAYLIST/*.mpls` structure. It doesn't handle disc decryption, and a flattened single-file rip won't work — the `BDMV/PLAYLIST` folder needs to be intact.
- **No re-encoding.** Video and the chosen audio track are stream-copied, never transcoded. If you want a different codec or a lossy format, that's a separate step outside this tool's scope.
- **Every other audio track is dropped.** Only the one audio track you pick is kept in the output files; if you want more than one mix per song, that means running the job again with a different track selected.
- **Windows-focused distribution.** The pre-built `.exe` is Windows-only. Running from source depends only on CustomTkinter, mkvmerge, and ffmpeg, which do exist on macOS/Linux, but that path isn't officially tested or supported.

---

## 🐛 Reporting Issues

Found a bug or have a feature request? [Open an issue](https://github.com/quinnuk/DiscTrackSplitter/issues/new/choose) on GitHub. The app can take you there directly from its Help menu.

If something failed mid-extraction, attach the run's `extraction.log` (opens with one click from the app once a job has run) — it makes tracking down the problem much faster.

---

## ☕ Support This Project

DiscTrackSplitter is free and open source. If it saves you time, consider [buying the author a coffee](https://buymeacoffee.com/quinnuk).

---

## 📄 License

Released under the [MIT License](LICENSE).