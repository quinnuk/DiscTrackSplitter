<div align="center">

# 🎧 Disc Track Splitter

**Split a ripped Blu-ray concert/music disc into individual, chapter-named song files — using whichever audio track you choose — without ever re-encoding the audio.**

![Platform](https://img.shields.io/badge/platform-Windows-lightgrey)
![Python](https://img.shields.io/badge/python-3.10%2B-3776AB)
![License](https://img.shields.io/badge/license-MIT-green)

</div>

---

## Is this for you?

You've ripped a Blu-ray Audio disc — a concert film, a live album, or one of the growing number of immersive-audio reissue Blu-rays — and you want it in your **Plex / Jellyfin / Kodi** library as individual, correctly-named song files instead of one giant feature-length file. If that's you, this tool exists for exactly that job, and nothing else.

It is **not** a ripping tool, a re-encoder, or a general-purpose media converter — see [Notes & known limitations](#notes--known-limitations) for what it deliberately doesn't do.

This is a sibling project to [AtmosTrackSplitter](https://github.com/quinnuk/AtmosTrackSplitter). That tool only ever extracts the Dolby Atmos track. This one asks first: it lists every audio track a playlist actually has — Atmos, TrueHD, DTS-HD Master Audio, LPCM, in whatever channel layouts (2.0/5.1/7.1) and sample rates the disc offers — and lets you pick. Atmos is still pre-selected automatically when present, so the common case behaves exactly the same; the difference shows up on discs that don't have Atmos at all, like Pure Audio / Surround Series releases with multiple LPCM/DTS-HD mixes and no object-based track.

## Table of Contents

- [Why this exists](#why-this-exists)
- [Features](#features)
- [How it works](#how-it-works)
- [Requirements](#requirements)
- [Usage](#usage)
- [Building a standalone .exe](#building-a-standalone-exe)
- [Project layout](#project-layout)
- [Recovering from an interrupted run](#recovering-from-an-interrupted-run)
- [Notes & known limitations](#notes--known-limitations)
- [Reporting Issues](#reporting-issues)
- [License](#license)

## Why this exists

Concert Blu-rays and hi-res reissue discs are usually one giant file with chapter markers per song, and often carry two or more versions of the mix — a stereo LPCM master alongside a 5.1 DTS-HD MA mix, for example. Most tools either:
- flatten/transcode the audio (losing the format you actually wanted), or
- give you the whole disc as one file with no easy way to split it or pick a track, or
- require you to hand-build `mkvmerge`/`ffmpeg` commands per chapter.

Disc Track Splitter automates the whole thing: point it at the ripped disc folder, pick which audio track you want to keep, name the tracks (typed, pasted, imported, or looked up), and get one clean, bit-exact file per song.

## Features

- 🔍 **Auto-detects the right playlist** — scans every `.mpls` playlist and scores each one (Atmos track present, chapter count, video track, duration) rather than just grabbing the first match, and explains its reasoning so you can sanity-check the pick. The playlist dropdown only appears when there are genuinely two or more plausible candidates.
- 🎚️ **Audio track picker** — every audio track on the selected playlist is listed with its codec, channel layout, sample rate, bitrate, and bit depth (e.g. "DTS-HD Master Audio - 5.1, 96kHz, 8407kbps, 24-bit"). The Dolby Atmos track is pre-selected and starred when present; otherwise the best lossless option is pre-selected. Pick a different one any time — the stereo mix instead of 5.1, LPCM instead of DTS-HD MA, whatever the disc offers.
- 📝 **Four ways to name tracks** — paste a plain list and it fills the chapter table automatically; import a tracklist file (`.txt`, `.nfo`, `.cue`, or a saved `.tracklist.json`); pull in whatever chapter names are already embedded in the disc; or look the album up on **MusicBrainz**. Every method shows a review screen before anything is applied.
- 📂 **Finds sidecar tracklists for you** — if a `.txt`/`.nfo`/`.cue` tracklist is already sitting in the disc rip folder, the app tells you so you don't have to go looking.
- ⏸️ **Resumable, cancel-safe jobs** — extraction progress is checkpointed to a manifest (including which audio track was chosen), so a crash, cancel, or closed app mid-job doesn't mean starting over.
- 📋 **Per-job extraction log** — every run writes an `extraction.log` you can open with one click.
- ⚠️ **Checks for MKVToolNix/ffmpeg on startup** — a dialog with direct download links if either is missing.
- ✂️ **No re-encoding** — stream-copies video + your chosen audio track only; the mix stays bit-exact.
- 📺 **Keeps the video track** — so playback on a TV shows the concert, not a black screen.
- 📁 **Clean output, never overwritten silently** — a sensibly-named album folder, one file per song, confirmation before anything existing is replaced.
- 💾 **Remembers your settings** — last-used folders and tool paths persist between runs.
- ⚡ **Scans as you go** — point the source field at a disc folder and it scans automatically.
- 🪟 **Runs standalone** — build a windowed `.exe` with no attached console.
- ❓ **Built-in Help menu** — tips and troubleshooting live in the app itself.

## How it works

1. Point the app at a folder containing a ripped disc — it must have the standard, unmodified `BDMV/PLAYLIST/*.mpls` structure (not a flattened single MKV).
2. It inspects every playlist via `mkvmerge -i` and scores each as a candidate for the main feature, pre-selecting the best match and showing its reasoning.
3. It lists every audio track on the selected playlist and pre-selects Atmos if present, or the best lossless option otherwise. Pick a different track if you'd rather have it.
4. You name each chapter — paste, import a file, pull in embedded names, or look it up on MusicBrainz — and review the proposed matches before accepting them.
5. It extracts the video track plus just your chosen audio track (stream-copy, no transcoding) via `mkvmerge`, dropping every other audio track on the playlist. It then reads the exact chapter timestamps and splits the result into one file per song with `ffmpeg`, checkpointing progress as it goes.

## Requirements

**External tools** (must be installed and either on your `PATH`, or their paths set via the app's missing-tools dialog / `settings.py`):

| Tool | Provides | Link |
|---|---|---|
| MKVToolNix | `mkvmerge`, `mkvextract` | https://mkvtoolnix.download/ |
| ffmpeg | `ffmpeg`, `ffprobe` | https://ffmpeg.org/ |

**Python packages:**

```bash
pip install -r requirements.txt
```

## Usage

```bash
python main.py
```

> **Note:** if you launch it from PowerShell/cmd, the app is a child process of that terminal — closing the terminal window kills the app too, even mid-extraction. Run it with `pythonw main.py` (fully detached, no console) instead, or build the standalone `.exe` below.

## Building a standalone .exe

```bash
pip install pyinstaller
build_exe.bat
```

This produces `DiscTrackSplitter.exe` — a windowed exe with no attached console.

> The exe bundles the Python app itself, but **not** `mkvmerge`, `mkvextract`, `ffmpeg`, or `ffprobe` — those still need to be installed separately and reachable on `PATH`.

## Project layout

```
DiscTrackSplitter/
├── main.py                    GUI (customtkinter): scanning, track picking, naming,
│                                extraction, Help menu
├── extractor.py                Core logic: scanning, scoring, track enumeration,
│                                chapters, tracklist matching, MusicBrainz lookup,
│                                splitting, resume/manifest handling
├── settings.py                 Persisted settings (tool paths, last-used folders)
├── split_now.py                 CLI: split an already-extracted MKV by hand
├── DiscTrackSplitter.spec      PyInstaller build spec
├── build_exe.bat               One-click .exe build script
└── requirements.txt
```

`extractor.py` has no GUI dependencies, so it can be imported and used on its own.

## Recovering from an interrupted run

If the app is closed, crashes, or a job is cancelled partway through, just point it at the same source and output folders again — it'll detect the in-progress manifest and offer to **resume** from wherever it left off, using the same audio track that job started with.

If you'd rather handle it by hand, the intermediate `_audio_extracted.mkv` is left in the work folder rather than being cleaned up automatically on failure. Split it directly:

```bash
python split_now.py "path\to\_audio_extracted.mkv" "path\to\output folder" --names-file tracks.txt
```

`tracks.txt` is just one track name per line, in chapter order. Leading numbering (`1.`, `01 -`) is stripped automatically. Add `--list-only` (and skip `--names-file`) to just see how many chapters a file has before committing to names.

## Notes & known limitations

- Assumes chapters map 1:1 to songs. This holds for most concert/live-album Blu-rays, but always sanity-check the chapter count against the actual tracklist before running the paste-to-fill step.
- If a disc has no audio tracks, or the audio isn't chaptered per song, this tool won't help.
- **Bitrate shown in the track picker is best-effort.** mkvmerge doesn't report bitrate directly; it's derived exactly for uncompressed LPCM, and via `ffprobe` (when it's reported) for compressed formats like DTS-HD MA. If neither source has it, the bitrate is simply left off that track's label rather than guessed.
- **MusicBrainz coverage is best for mainstream releases.** Small-run audiophile Blu-ray Pure Audio / Surround Series discs and other boutique/mail-order-only editions are often missing from MusicBrainz entirely. Use Import Tracklist with a sidecar file instead if that happens.
- Output format defaults to `.mkv` to preserve the audio track's original metadata — converting to a plain audio container like FLAC would discard it for object-based formats like Atmos.
- Folder-in, folder-out only — no CLI or disc-drive/ripping support. This tool works on discs you've **already ripped yourself**; it doesn't rip or decrypt anything.

## Reporting Issues

Found a bug or have a suggestion? Please open an issue rather than a Reddit comment, so it doesn't get lost.

The most useful things to include: the `mkvmerge -i` output for the affected playlist, and the `extraction.log` from the job (Open Log button in the app, or the file directly in your output folder).

## License

Released under the [MIT License](LICENSE). You are free to use, modify, and share this software.
