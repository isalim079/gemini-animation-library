# 2D Animation Shorts Pipeline

A complete toolkit and workflow for creating vertical **9:16 (1080×1920, 24 fps)** 2D flat-vector animated YouTube Shorts—combining procedural Python/Pillow animation rendering, synthesized electronic sound design (or neural TTS voiceover with `librosa` word-aligned captions), and YouTube-ready `-14 LUFS` FFmpeg mastering.

---

## Repository Structure

```text
2D_animation_test/
├── README.md                   # Installation & usage guide (this file)
├── DOCUMENTATION.md            # Pipeline architecture & compliance checklist
├── master_prompt.md            # Reusable prompt template for generating new Shorts
├── .gitignore                  # Excludes rendered videos, frames, audio & virtualenvs
├── timber-shorts-skill/        # Reusable agent skill (SKILL.md) for 2D Shorts
├── lemo-opuscar/               # Style library, audio/render helpers & mux.sh
├── librosa/                    # Speech-energy analysis for word-level caption timing
├── rea-video/                  # Shot-by-shot reference video analyzer (VIDEO.md)
└── videos/                     # (Git-ignored) Rendered MP4s & per-project assets
```

---

## Minimum System Requirements

| Component | Minimum | Recommended |
| :--- | :--- | :--- |
| **OS** | macOS 12+, Ubuntu 20.04+ / Debian 11+, or Windows 10 (64-bit) | macOS 14+, Ubuntu 22.04+, or Windows 11 |
| **CPU** | 4-core 64-bit processor (x86_64 or ARM64) | 8-core+ (Apple Silicon M-series / Ryzen 5 / Core i5+) |
| **RAM** | 8 GB | 16 GB (for faster 2× supersampled frame rendering) |
| **Storage** | 2 GB free disk space (tools + 720–960 PNG frames per Short) | 10 GB+ SSD free space |
| **GPU** | Not required (CPU-based Pillow + FFmpeg rendering) | Optional |
| **Software** | Python 3.10+, FFmpeg 5.0+, Git | Python 3.12+, FFmpeg 7.0+ |

---

## Installation

### 1. Clone the Repository
```bash
git clone <your-repo-url>
cd 2D_animation_test
```

### 2. Install FFmpeg
* **macOS (Homebrew):**
  ```bash
  brew install ffmpeg
  ```
* **Linux (Ubuntu / Debian):**
  ```bash
  sudo apt update && sudo apt install -y ffmpeg
  ```
* **Windows (PowerShell via `winget` or `choco`):**
  ```powershell
  winget install Gyan.FFmpeg
  # or: choco install ffmpeg
  ```
  *(Restart your terminal after installing so `ffmpeg` and `ffprobe` are on your `PATH`.)*

Verify installation on any OS:
```bash
ffmpeg -version
ffprobe -version
```

### 3. Set Up the Python Environment
Create and activate a virtual environment inside `lemo-opuscar/.venv` and install the core rendering and audio packages:

* **macOS / Linux:**
  ```bash
  python3 -m venv lemo-opuscar/.venv
  source lemo-opuscar/.venv/bin/activate
  pip install --upgrade pip
  pip install pillow numpy scipy soundfile edge-tts qwen-tts
  ```

* **Windows (PowerShell):**
  ```powershell
  py -3 -m venv lemo-opuscar\.venv
  .\lemo-opuscar\.venv\Scripts\Activate.ps1
  python -m pip install --upgrade pip
  python -m pip install pillow numpy scipy soundfile edge-tts qwen-tts
  ```
  *(If PowerShell blocks script execution, run `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned` once, or use Command Prompt: `lemo-opuscar\.venv\Scripts\activate.bat`.)*

* **Optional (Node-based helpers in `lemo-opuscar/`):**
  ```bash
  cd lemo-opuscar
  npm install
  cd ..
  ```

---

## How to Use This Project for Animation

You can use this project in **two ways**: **pure procedural 2D animation** (character + cinematic SFX/music, no narration) or **narrated Shorts** (neural TTS + `librosa` word-aligned pop captions).

### Workflow A: Procedural 2D Animation + Synthesized Electronic Audio
Ideal for visual storytelling Shorts (e.g., *T1 Turns ON*) with custom 2D vector characters, camera movement, and synchronized electronic sound design.

1. **Create a project folder** under `videos/YYYY-MM-DD/<project_slug>_project/`:
   ```bash
   mkdir -p videos/2026-11-16/my_short_project/frames
   ```
2. **Write your frame renderer (`render_video.py`):**
   * Render at **2× supersampling** (`2160×3840`) with Pillow (`ImageDraw`) and downsample with `Image.LANCZOS` to **`1080×1920`** for crisp, anti-aliased 2D flat-vector outlines.
   * Export sequential PNGs (`frames/frame_0000.png` … `frames/frame_0719.png` for a 30-second video at `24 fps`).
   * Use `concurrent.futures.ThreadPoolExecutor` for fast multi-core rendering.
3. **Synthesize synchronized audio (`synth_audio.py`):**
   * Generate a `48000 Hz` stereo `mix.wav` using `numpy`, `scipy.signal`, and `soundfile` timed to exact scene timestamps (hum, pulses, clicks, risers, impacts).
4. **Master the final YouTube-ready MP4:**
   ```bash
   ffmpeg -y -framerate 24 -i frames/frame_%04d.png -i mix.wav \
     -c:v libx264 -preset medium -crf 18 -pix_fmt yuv420p \
     -af "loudnorm=I=-14:TP=-1.0:LRA=11" \
     -c:a aac -b:a 192k -ar 48000 \
     -movflags +faststart -shortest ../My_Short.mp4
   ```

---

### Workflow B: Narrated Shorts with Word-Aligned Pop Captions
Ideal for 40-second story/explainer Shorts (see [`DOCUMENTATION.md`](./DOCUMENTATION.md) and [`master_prompt.md`](./master_prompt.md)).

1. **Generate Narration (`qwen-tts` or `edge-tts`):**
   * **Qwen3-TTS (Local / Expressive / Voice Clone):** Use [`Qwen3-TTS`](https://github.com/QwenLM/Qwen3-TTS) (`qwen-tts`) for expressive narration (`Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice` or `0.6B-CustomVoice` with speakers like `Ryan` or `Aiden`), custom character voice design (`VoiceDesign`), or 3-second voice cloning (`Base`).
   * **Edge-TTS (Fast Cloud Fallback):** Or generate clean voiceover clips (`en-US-AndrewNeural`, `rate="-4%"`) and assemble a `40.0s` `48 kHz` stereo `mix.wav`.
2. **Extract Speech Energy (`librosa`):**
   * Compute per-frame vocal RMS (`rms_24fps`) and map word timestamps into `word_timings.json` so pauses and emphasis are respected.
3. **Render Frames & Captions:**
   * Draw 2D cartoon scenes and render 3-word caption chunks in the lower safe zone (`y = 1480`), applying an `ease_out_back` pop to the active word scaled by `rms_24fps`.
4. **Master with `mux.sh`:**
   ```bash
   bash lemo-opuscar/core/render/mux.sh \
     --video out/video.mp4 \
     --audio mix.wav \
     --out ../My_Narrated_Short.mp4
   ```
   `mux.sh` runs two-pass EBU R128 loudness normalization to **`-14.0 LUFS`** with true-peak limiting (`<= -1.0 dBFS`).

---

### Workflow C: Reverse-Engineering Reference Videos (`rea-video`)
To study pacing, shot lengths, and visual composition from an existing Short:
```bash
rea-video decompose path/to/reference.mp4
```
This outputs a shot-by-shot breakdown (`VIDEO.md` and `report.html`) you can feed directly into [`master_prompt.md`](./master_prompt.md).

---

## Output Specifications (YouTube Shorts Standard)

| Property | Value |
| :--- | :--- |
| **Resolution** | `1080 × 1920` (9:16 vertical) |
| **Frame Rate** | `24 fps` |
| **Video Codec** | H.264 (`libx264`, `yuv420p`, `crf 18`) |
| **Audio Codec** | AAC `192 kbps`, `48000 Hz`, Stereo |
| **Loudness** | `-14.0 LUFS` integrated, `<= -1.0 dBFS` true peak |

---

## Git & Storage Notes

Rendered outputs (`videos/`, `frames/`, `out/`, `*.mp4`, `*.wav`) and local environments (`.venv/`, `node_modules/`) are excluded in [`.gitignore`](./.gitignore) so the repository stays lightweight and clean on GitHub.
