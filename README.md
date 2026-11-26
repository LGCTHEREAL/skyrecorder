<p align="center">
  <img src="icons/skyrecorder.png" width="140" alt="SkyRecorder logo">
</p>

<h1 align="center">SkyRecorder</h1>

<p align="center">
  <b>Spotify → OBS automatic recorder</b> with cover art &amp; metadata<br>
  One file per song · MP3 / WAV / FLAC · Linux &amp; Windows
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10%2B-3776AB?logo=python&logoColor=white" alt="Python 3.10+">
  <img src="https://img.shields.io/badge/platform-Linux%20%7C%20Windows-lightgrey" alt="Linux | Windows">
  <img src="https://img.shields.io/badge/export-MP3%20%7C%20WAV%20%7C%20FLAC-orange" alt="MP3 | WAV | FLAC">
</p>

Play a song on Spotify → SkyRecorder starts an OBS recording **automatically**
(it even opens OBS for you if it's closed) → when the song ends, the video is
renamed and exported as **one file per song**, tagged with the album, track
number, year and the cover art you see in Spotify.

```text
[>]   play               → recording starts (OBS opened automatically if closed)
[||]  pause / [>] resume → recording pauses / resumes (one single file, no cuts)
[>>]  track change       → stop → rename → export MP3/WAV/FLAC + tags + cover
[x]   ad                 → recording stops
```

## Features

- **One file per song** — every track becomes `Artist - Title.mp3` (the raw
  `.mp4` stays next to it)
- **OBS opens itself** — if OBS isn't running, SkyRecorder launches it at
  startup and before every track, so no song is ever missed
- **Pause follows Spotify** — pause, resume or skip: the recording follows and
  keeps a single clean file
- **Ad detection** — ads stop the recording instead of polluting your library
- **Full metadata & cover art** — album, track number, year… from Spotify live
  with MusicBrainz as fallback; cover from Spotify → MusicBrainz → iTunes →
  Deezer. **No API keys needed.**
- **OBS performance mode** — first launch applies a VRAM-friendly 720p30
  profile to avoid encoder crashes, with a quality toggle in the app
- **Linux & Windows**

## Requirements

- Spotify desktop app
- OBS Studio
- Python 3.10+ (the installers take care of the rest)

## Install

### Linux

1. Download and extract `skyrecorder-installer-linux.tar.gz`
2. Double-click `installer.desktop` (or run `bash install.sh` in a terminal)
3. Find **SkyRecorder** in your app menu

### Windows *(preview — testing in progress)*

1. Download and extract `skyrecorder-installer-windows.zip` (opens with a
   double-click, no extra tools needed)
2. Double-click **`install.bat`** — it checks Python, installs the
   dependencies, installs ffmpeg (via winget) and puts a SkyRecorder shortcut
   on your desktop
3. Done — the app launches when installation finishes

## First run (once)

**OBS (Linux & Windows)**

1. Open OBS Studio → **Tools → WebSocket Server Settings**
2. Enable the server and set a password
3. Start SkyRecorder with it: `skyrecorder --password <your-password>`
   (on Windows: edit `launch.bat` and add `--password <your-password>` after
   `main.py`, or leave it empty if OBS has no password)

**Spotify (Windows only)**

SkyRecorder reads your playback through the Spotify Web API, so Windows asks
for API credentials once:

1. Go to <https://developer.spotify.com/dashboard/> and click **Create App**
2. Name: `SkyRecorder` — Redirect URI: `http://localhost:8888/callback`
3. Copy the **Client ID** and **Client Secret** when SkyRecorder asks for them

On Linux nothing is needed — playback is read directly from the Spotify app.

## Use

1. Open SkyRecorder — it opens OBS by itself if it isn't running
2. Play any song on Spotify — recording starts by itself
3. Pause/resume/skip — the recording follows (pause keeps one single file)
4. Each finished song is exported next to your recordings, tagged and with
   cover art

Choose the export format(s) in the app, or on the command line:

```bash
skyrecorder --format mp3 flac
```

- **MP3** — VBR q0 (up to 320 kbps) + tags + cover art
- **WAV** — PCM 24-bit lossless
- **FLAC** — lossless + tags + cover art

Metadata comes from Spotify itself (live), with MusicBrainz as fallback — no
API keys needed.

## Project structure

```text
skyrecorder/
├── main.py                    entry point
├── requirements.txt
├── install.sh / install.bat   installers (Linux / Windows)
├── launch.bat                 Windows launcher
├── uninstall.sh               Linux uninstaller
├── icons/
└── skyrecorder/
    ├── ui.py                  GTK window (Linux)
    ├── console_ui.py          console UI (Windows)
    ├── monitor.py             Spotify playback monitor — MPRIS (Linux)
    ├── web_monitor.py         Spotify playback monitor — Web API (Windows)
    ├── obs_controller.py      OBS recording control (start/stop/rename)
    ├── obs_profile.py         OBS settings profiles (performance/normal)
    ├── audio_export.py        MP3 / WAV / FLAC export
    ├── spotify_meta.py        metadata + cover art lookup
    └── ad_detector.py         ad detection
```

## Troubleshooting

- **No recording starts** — make sure the OBS WebSocket server is enabled
  (**Tools → WebSocket Server Settings**) and that SkyRecorder was started
  with `--password <your-password>` if OBS has one.
- **Windows doesn't detect playback** — complete the Spotify API setup
  (Client ID / Client Secret) the app asks for on first run.

## Uninstall

- **Linux:** `bash uninstall.sh` in the extracted folder (or the SkyRecorder
  entry in your app menu)
- **Windows:** delete the SkyRecorder folder and the desktop shortcut

## Changelog

See [CHANGELOG.md](CHANGELOG.md).

