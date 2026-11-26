[CHANGELOG.md](https://github.com/user-attachments/files/35468971/CHANGELOG.md)
# Changelog

## v2.2 — 2026-11-25

### Added
- **OBS auto-start** — if OBS isn't running, SkyRecorder opens it itself (at monitoring start and before every track). No more missed songs because OBS was closed.
- Recording start now **retries up to 3 times** with a fresh connection — a transient websocket hiccup no longer loses a track.

### Fixed
- `'NoneType' object has no attribute 'start_record'` when OBS was closed or restarted mid-session (stale OBS client) — the main cause of missing per-song files.
- First launch no longer kills a manual OBS recording: if OBS is recording (or can't be checked), profile settings are deferred to the next start.
- Profile apply aborts with a clear message when OBS refuses to stop, instead of silently launching a duplicate instance whose exit would overwrite the settings.
- OBS detection now covers `obs64.exe`, `obs32.exe` and `obs.exe` (Windows).

### Windows
- Console Ctrl+C / window close always triggers a clean shutdown, including on first run; the shutdown handler is idempotent.
- The Spotify OAuth callback wait really times out now (was an infinite loop if the browser was abandoned); a busy port 8888 reports a clear error instead of crashing setup.
- A corrupted `spotify_config.json` falls back to the setup wizard instead of crashing.
- Token/network failures back off (60 s) instead of flooding the log every 2 s while offline.
- INI reads tolerate a UTF-8 BOM; recording rename retries against transient file locks (Defender).
- EOF (Ctrl+Z) during credential entry exits cleanly instead of raising a traceback.
- Installer clarifies the Microsoft Store Python-alias case and mentions the OBS `--password` hint.

### Changed
- Recording-start failures now distinguish encoder errors from "OBS unreachable" in the log.

---

## v2.1 — 2026-11-09

### Added
- **Windows support** — double-click `install.bat`: checks Python 3.10+, installs dependencies, installs ffmpeg via winget if missing, creates a desktop shortcut, then launches. Console UI, Spotify Web API playback monitor (pause/resume included), and OBS profile control all work on Windows (`%APPDATA%\obs-studio`).
- First-run OBS performance mode (720p30) now auto-applies on Windows too.

### Removed
- **AcoustID entirely** — fingerprint matching, API key storage, the key button/dialog in the UI, and the chromaprint (fpcalc) install step. The metadata chain is now: Spotify live → MusicBrainz → Spotify search (zero configuration).

### Fixed
- Windows first run crashed on `set_acoustid` (console UI never had it) — gone with AcoustID.
- `pip install -r requirements.txt` failed on Windows because of PyGObject — now marked `sys_platform != "win32"`.

### Changed
- Status line reads `metadata via Spotify live → MusicBrainz, cover art (Spotify live → MusicBrainz → iTunes → Deezer)`.

---

## v2.0 — 2026-10-13

### Added
- **Spotify live metadata** — album, cover art, track/disc numbers pulled directly from Spotify (MPRIS). No AcoustID key needed anymore.
- **Cover art priority**: Spotify live → MusicBrainz → iTunes → Deezer. The cover you see in Spotify is always the one embedded.
- **OBS quality toggle** — switch between Performance (720p30) and Normal (1080p60) from the UI. Audio stays max (320kbps stereo) in both modes. Auto-applies on first launch.
- **Encoder health check** — detects GPU/VRAM encoder failures at recording start, logs a clear warning, and retries on the next track instead of silently producing a dead file.
- **FLAC and WAV export** alongside MP3 (VBR q0 ≈ 320kbps).
- Export log now shows metadata source per file (e.g. `MP3 exported (Spotify (live))`).

### Fixed
- **Pause now instant** — was 4–6 s delay (or never triggered). PlaybackStatus signal is now trusted directly. Resume is instant too.
- **Pause no longer cancelled by last position update** — Spotify emits one final position bump after Pause; baseline is now reset to ignore it.
- **ResumeRecord 503 crash** — if OBS recording died while paused, the state is now cleaned up and recording restarts cleanly.
- Duplicate recordings get `(2)`, `(3)`… suffixes instead of overwriting.
- MusicBrainz cover ranking — official releases prioritized over bootlegs.
- AcoustID invalid key shows a one-time warning instead of failing silently.
- Stop-recording race when monitor shuts down mid-track.

### Changed
- Metadata chain simplified: Spotify live (primary) → AcoustID → MusicBrainz (fallback only).
- OBS profile editing is now line-based (preserves INI formatting, handles missing keys).
- OBS restart uses `--disable-shutdown-check` + sentinel cleanup (no more Safe Mode dialog).

---

## v1.0 — 2026-09

- Initial release
- Spotify → OBS auto-recording via MPRIS
- MP3 export with MusicBrainz metadata + cover art
- AcoustID fingerprint matching
- Linux (Fedora/Bazzite/Debian/Ubuntu), GUI installer (zero commands)
