# 🎧 DiscTrackSplitter

<p align="center">
  <strong>Split ripped Blu-ray concert & music discs into chapter-named song files, preserving your choice of audio track without re-encoding.</strong>
</p>

<p align="center">
  A lightweight GUI utility for Windows that safely extracts and splits Blu-ray concert and music tracks without quality loss.
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
  <sub>If DiscTrackSplitter helps organise your concert library, a coffee is always appreciated ☕</sub>
</p>

<p align="center">
  <img src="screenshot.png" alt="DiscTrackSplitter application screenshot" width="900">
</p>

---

## 📑 Table of Contents

- [Is This For You?](#-is-this-for-you)
- [Why This Exists](#-why-this-exists)
- [Key Features](#-key-features)
- [How It Works](#️-how-it-works)
- [Getting Started](#-getting-started)
- [Usage](#-usage)
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

It is **not** a ripping tool, a re-encoder, or a general-purpose media converter — see [Notes & known limitations](#-notes--known-limitations) for what it deliberately doesn't do.

> **Sibling Project:** This is a sibling project to AtmosTrackSplitter. While that tool only ever extracts the Dolby Atmos track, **DiscTrackSplitter** lists *every* audio track a playlist actually has — Atmos, TrueHD, DTS-HD Master Audio, LPCM, in whatever channel layouts (2.0 / 5.1 / 7.1) and sample rates the disc offers — and lets you pick.

---

## 💡 Why This Exists

Concert Blu-rays and hi-res reissue discs are usually one giant file with chapter markers per song, and often carry two or more versions of the mix — a stereo LPCM master alongside a 5.1 DTS-HD MA mix, for example. 

Most existing tools either:
- **Flatten/transcode** the audio (losing the format you actually wanted), or
- Give you the **whole disc as one file** with no easy way to split it or pick a track, or
- Require you to **hand-build complex `mkvmerge` / `ffmpeg` commands** per chapter.

**DiscTrackSplitter** automates the whole thing: point it at the ripped disc folder, pick which audio track you want to keep, name the tracks (typed, pasted, imported, or looked up), and get one clean, bit-exact file per song.

---

## ✨ Key Features

| | Feature | Description |
|---|---|---|
| 🔍 | **Auto-detects Playlist** | Scans every `.mpls` playlist, scores candidates (Atmos presence, chapter count, duration), and displays its reasoning. |
| 🎚️ | **Audio Track Picker** | Displays codec, channels, sample rate, bitrate, and bit depth. Pre-selects Dolby Atmos or best lossless mix automatically. |
| 📝 | **4 Track Naming Methods** | Paste plain lists, import files (`.txt`, `.nfo`, `.cue`, `.json`), pull embedded disc chapter names, or search **MusicBrainz**. |
| 📂 | **Sidecar Tracklist Detection** | Automatically flags matching tracklists already sitting in your disc rip folder. |
| ⏸️ | **Resumable Jobs** | Progress is checkpointed to a manifest so mid-job crashes or cancels won't lose your progress. |
| 📋 | **Per-Job Logging** | Every run writes an `extraction.log` accessible with a single click. |
| ✂️ | **Zero Re-encoding** | Stream-copies video and chosen audio only — bit-exact audio quality with TV video playback retained. |
| ⚡ | **On-the-Fly Scanning** | Automatically scans source directories upon selection. |
| 🪟 | **Standalone Executable** | Can be built as a clean, windowed `.exe` with no attached console. |

---

## 📸 Screenshot

<p align="center">
  <img src="screenshot.png" alt="DiscTrackSplitter interface" width="900">
</p>

---

## ⚙️ How It Works

1. **Point** the app at a folder containing a ripped disc (must have standard, unmodified `BDMV/PLAYLIST/*.mpls` structure).
2. **Inspects** every playlist via `mkvmerge -i`, scoring candidates for the main feature and showing its reasoning.
3. **Lists** all audio tracks, pre-selecting Atmos or the best available lossless stream. Choose a different track anytime!
4. **Name** chapters using paste, import, embedded names, or MusicBrainz lookup, then review proposed matches before accepting.
5. **Extracts** video + chosen audio via `mkvmerge` (stream-copy, no transcoding) and splits into individual chapter files using `ffmpeg`.

---

## 🚀 Getting Started

### 📦 Option 1 — Pre-compiled EXE

**[⬇️ Download the latest release](https://github.com/quinnuk/DiscTrackSplitter/releases/latest)**

1. Download `DiscTrackSplitter.exe`.
2. Run it — **no Python installation is required**.
3. Install the required external tools listed below if they are not on your `PATH`.

---

### 🐍 Option 2 — Run from Source

Install the required Python packages:

```bash
pip install -r requirements.txt