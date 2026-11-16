# Master Prompt — Eye-Soothing 2D Animated YouTube Short Generator

Copy and paste this prompt whenever you want to turn a new story into a 40-second YouTube Short in the exact *Timber*-inspired style:

---

```text
Create a 40-second vertical YouTube Short (1080x1920, 24 fps) using my `timber-shorts` workflow inside `/Volumes/Salim_EX/salim/2D_animation_test/videos/<TODAY_YYYY-MM-DD>/`.

STORY / TOPIC:
[PASTE YOUR STORY OR TOPIC HERE]

REQUIREMENTS:
1. Research & Fact-Check: If the story involves real people, companies, or events, verify key facts and dates first via Google/YouTube search. Keep everything factual and respectful.
2. 5-Scene Script (~60-75 words total): Break the story into 5 tight scenes across 40.0 seconds.
3. Eye-Soothing 2D Cartoon Style:
   - Use soft, muted watercolor/paper-textured backgrounds (misty slate, twilight, calm storm, soft dawn) with gentle sage (#789E6B), warm amber (#D4A36A), and muted terracotta (#C8645B) accents—no harsh neon colors or heavy grain.
   - Draw expressive 2D characters with thick dark ink outlines (#1A1C20), oversized Timber-style eyes with periodic blinking and mood eyelids, and stick limbs.
4. Natural Voiceover Only (Qwen3-TTS or Edge-TTS):
   - Generate natural neural narration using **Qwen3-TTS** (`qwen-tts` — e.g., `Qwen/Qwen3-TTS-12Hz-1.7B-CustomVoice` / `0.6B-CustomVoice` with speakers like `Ryan` or `Aiden`, `VoiceDesign`, or `Base` voice cloning) OR `edge-tts` (`en-US-AndrewNeural`, rate `-4%`) for all 5 scenes.
   - Do NOT add irritating or repetitive background music.
5. Librosa-Synced Single-Word Pop Captions:
   - Align word timings and vocal RMS energy using `librosa` on the voiceover.
   - Display 3-word rolling caption chunks at y=1480.
   - Keep non-focused words completely still on the baseline; ONLY the currently focused word performs a clean, single one-shot pop (no continuous wave loop).
6. Master & Verify:
   - Mux using `lemo-opuscar/core/render/mux.sh` at `-14.0 LUFS` (`grain=0`) into `/Volumes/Salim_EX/salim/2D_animation_test/videos/<TODAY_YYYY-MM-DD>/<story_slug>.mp4`.
   - Visually inspect sample frames before delivering.
7. YouTube Metadata (Monetization & Policy Safe):
   - Provide an SEO-friendly Title (<70 chars), Description, and 5-8 Hashtags.
   - Ensure the video, title, description, and hashtags strictly follow YouTube Partner Program & Advertiser-Friendly Content Guidelines (no clickbait, no copyrighted assets, no defamation/harassment/misinformation).
```
