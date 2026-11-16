# 2D Animation Shorts Pipeline — Full Documentation

## 1. Installed Environment (`/Volumes/Salim_EX/salim/2D_animation_test`)
- **`lemo-opuscar/`**: Style library, rendering/audio tools, and `core/render/mux.sh` (two-pass `-14 LUFS` YouTube loudness mastering via FFmpeg 9.0.2). Uses `.venv` (Python 3.12 + `qwen-tts`, `edge-tts`, `numpy`, `pillow`, `scipy`, `soundfile`).
- **`Qwen3-TTS/`**: Local Qwen3-TTS repository (`qwen-tts` installed in editable mode into `lemo-opuscar/.venv`) for expressive narration, voice design, and voice cloning.
- **`rea-video/`**: Video reverse-engineering CLI (`rea-video decompose`) used to analyze reference videos shot-by-shot into `VIDEO.md` and `report.html`. Uses `.venv` (Python 3.12 + `opencv-python-headless`, `onnxruntime`, `rapidocr-onnxruntime`).
- **`librosa/`**: Audio analysis library (`1.1.0dev` on Python 3.13 `.venv`) used to compute vocal RMS energy envelopes and align word-level caption timings.
- **`timber-shorts-skill/SKILL.md`**: Reusable agent skill encoding the complete workflow.
- **`master_prompt.md`**: Ready-to-use prompt template for generating new shorts from any story.

## 2. Folder Structure Per Video
```text
videos/
└── YYYY-MM-DD/
    ├── <video_name>.mp4          # Final mastered 1080x1920, 24fps, -14 LUFS MP4
    └── <slug>_project/
        ├── voice/                # 01.wav..05.wav (Qwen3-TTS or edge-tts)
        ├── mix.wav               # Assembled 40.0s 48kHz stereo voiceover
        ├── audio_sync.json       # librosa 24fps RMS & beat data
        ├── word_timings.json     # librosa speech-energy aligned word timestamps
        ├── render_timber.py      # 2D cartoon frame renderer (Pillow -> FFmpeg)
        └── out/                  # Raw video.mp4 & verification still frames
```

## 3. How the Pipeline Works
1. **Reference Reverse-Engineering (`rea-video`):** Extracted 67 shots from *Timber* (`mBZBPptSneM`), identifying 24 fps pacing, locked framing, muted paper-textured palettes, and oversized expressive eyes/eyelids with stick limbs.
2. **Natural Voiceover (`qwen-tts` / `edge-tts`):** Generates expressive local neural narration, custom character voices, or 3-second voice clones via **Qwen3-TTS** (`Qwen/Qwen3-TTS-12Hz-1.7B/0.6B-CustomVoice`, `VoiceDesign`, or `Base`), or fast cloud narration via `edge-tts` (`en-US-AndrewNeural`, `rate="-4%"`), with zero distracting background loops.
3. **`librosa` Word Alignment & Pop Emphasis:** Computes per-frame vocal RMS (`rms_24fps`) and distributes word timestamps (`word_timings.json`) proportionally to actual speech energy so pauses are respected.
4. **Non-Looping Single-Word Pop Captions:** Renders 3-word chunks in the lower safe zone (`y = 1480`). Unfocused words remain stationary on the baseline; only the active word performs a single `ease_out_back` pop scaled by `librosa` RMS.
5. **Mastering (`mux.sh`):** Two-pass EBU R128 loudness normalization to `-14.0 LUFS` and true-peak limiting (`<= -1.0 dBFS`) for YouTube Shorts.

## 4. YouTube Monetization & Policy Compliance Checklist
- **Original Visuals & Audio:** 100% procedurally drawn 2D art and synthesized neural voiceover—zero reused clips or copyrighted music (safe from Reused Content / Content ID claims).
- **Advertiser-Friendly & Non-Defamatory:** Factual reporting only; no profanity, hate speech, harassment, or unverified accusations.
- **Metadata Hygiene:** Accurate, non-sensationalized titles (<70 chars), clear descriptions, and 5–8 relevant hashtags (no tag stuffing).

## 5. Reference Production Standard (`dtmU-KQitHE` — *SizedUnderstood*)
Reverse-engineered from high-retention tech explainer Shorts (`1:34`, `2.19M+` views):
- **Captions (Zero Eye Fatigue):** Display **2–4 words** per phrase in a clean sans-serif font on a locked horizontal baseline. Highlight the active word with a **thin yellow CAD-style selection frame (`#FACC15`) with corner anchor dots** while keeping unfocused words muted (`#9CA3AF`). Never bounce or reflow text vertically.
- **Audio & Music:** Keep **voiceover + crisp micro-SFX** (pops, ticks, counters, electrical hums) 100% dominant. Either omit background music entirely or use an ultra-subtle soft marimba/lo-fi pulse at **`-22` to `-25 dB`** under the voice—zero percussive kicks or bright synth arpeggios.
- **Continuous Visual Sync:** Never hold a static frame—pair continuous camera glides, macro zooms, and X-ray peel-aways with an immediate **UI callout card or counter** for every spoken noun or number, plus a persistent corner progress badge (e.g. running cost or score).

