---
name: timber-shorts
description: Create eye-soothing 40-second vertical (1080x1920) 2D hand-drawn cartoon YouTube Shorts with natural neural voiceover, librosa word-aligned single-word pop captions, and YouTube monetization-safe metadata.
---

# Timber-Style 2D Animated YouTube Shorts Skill

Use this skill whenever the user provides a story, topic, or news event and wants a 40-second vertical (`1080x1920`, `24 fps`) eye-soothing 2D animated YouTube Short with natural voiceover, `librosa`-synced single-word pop captions, and YouTube policy-safe metadata.

## Workspace & Output Convention
- Base directory: `/Volumes/Salim_EX/salim/2D_animation_test`
- Output folder: `/Volumes/Salim_EX/salim/2D_animation_test/videos/<YYYY-MM-DD>/`
- Project working folder: `videos/<YYYY-MM-DD>/<slug>_project/`
- Final deliverable: `videos/<YYYY-MM-DD>/<slug>.mp4`

## Step-by-Step Production Workflow

### 1. Story & Fact Verification
- If the topic involves real people, companies, or current events, verify all dates, figures, and claims via `google:search` and `youtube:search` before writing the script.
- Keep narration strictly factual, balanced, and non-defamatory so the video complies with YouTube's Advertiser-Friendly Content Guidelines and Partner Program policies.
- Break the story into **5 scenes** totaling **40.0 seconds** (~12–15 words per scene, ~60–75 words total).

### 2. Visual Style (Eye-Soothing *Timber* Aesthetic)
- **Palette:** Use soft, muted, eye-soothing watercolor/paper-textured backgrounds (misty slate-blue, warm dusk, calm twilight, soft dawn) with subtle low-frequency paper grain (`blotch * 3.0 + fine * 1.2`) and zero harsh neon colors.
- **Accents:** Muted sage green (`#789E6B`), warm amber (`#D4A36A`), and muted terracotta (`#C8645B`) for dramatic turns.
- **Characters:** Thick dark ink outlines (`#1A1C20`), oversized expressive cartoon eyes with periodic blinking and mood eyelids (`neutral`, `happy`, `proud`, `shocked`, `sad`), and clean stick limbs.
- **Safe Zones (`1080x1920`):** Keep top badges at `y = 210`, titles at `y = 340`, characters/props in `y = 500..1250`, and captions at `y = 1480` (above YouTube Shorts bottom UI).

### 3. Natural Voiceover (Qwen3-TTS or Edge-TTS — Zero Irritating Background Music)
- Generate 5 scene narration clips (`01.wav`–`05.wav`) using **Qwen3-TTS** (`qwen-tts` — `Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice` or `0.6B-CustomVoice` with speakers like `Ryan`/`Aiden`, `VoiceDesign` for custom character voices, or `Base` for 3-second voice cloning) OR `edge-tts` (`en-US-AndrewNeural`, `rate="-4%"`).
- Convert to 48 kHz mono WAV, trim leading/trailing silence, and assemble onto a 40.0s stereo `mix.wav` with clean pauses between scenes. Do **not** add repetitive or distracting background loops.

### 4. `librosa` Speech-Energy Analysis & Single-Word Pop Captions
- Run `librosa/.venv/bin/python` on `mix.wav` (`audio_sync.json`) and on each `voice/0*.wav` (`word_timings.json`) using `librosa.feature.rms` to distribute word timings proportionally to actual spoken energy.
- Render captions in **3-word rolling chunks**:
  - **Non-focused words:** Stay completely still on the baseline (`base_y = 1480`) with crisp dark outlines and soft drop shadows.
  - **Focused word only:** Performs a single, non-looping one-shot pop (`ease_out_back` over `0.14s`, lifted by `10 + 12 * rms` px) inside a rounded highlight pill. Never wave all words in a continuous loop.

### 5. Render, Master & Verify
- Render 960 frames (`1080x1920`, `24 fps`) via Python `Pillow` piped into `ffmpeg` (`libx264`, `crf 16`, `yuv420p`).
- Master audio & video using `lemo-opuscar/core/render/mux.sh <video.mp4> <mix.wav> <out.mp4> 24 0` (`-14.0 LUFS`, true peak `<= -1.0 dBTP`, `grain=0`).
- Verify streams with `ffprobe` and visually inspect at least 2–3 extracted frames with `view_file`.

### 6. YouTube Policy & Monetization-Safe Metadata
Always output after rendering:
- **Title:** Under 70 characters, factual, no all-caps clickbait, no misleading claims.
- **Description:** 2–3 concise sentences summarizing the story, an engagement question, and clean attribution/context.
- **Hashtags:** 5–8 relevant, non-spammy hashtags (`#Shorts`, `#YouTubeShorts`, plus topic tags).
- **Policy Check:** Confirm zero copyrighted audio/footage, zero hate speech/harassment/misinformation, and advertiser-friendly language.
