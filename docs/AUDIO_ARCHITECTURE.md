# Bé Bum — hybrid Vietnamese audio integration

## Status
The game client now routes all narration through `speakGameVietnamese(text, options)`.
The audio system is **architecturally integrated, not yet production-voiced**: no prerecorded Southern Vietnamese MP3 files or deployed TTS service credentials are included. GitHub Pages is static and cannot safely store an AI provider secret.

## Priority
1. Prerecorded `audio/*.mp3` path in `window.BUM_AUDIO_MANIFEST`, if configured and playable.
2. HTTPS `window.BUM_AUDIO_CONFIG.endpoint` backend, using POST JSON: `{"text":"...","voice":"vi-south","purpose":"sao-do|proverb|game"}`. Backend returns JSON `{"audioUrl":"https://.../file.mp3"}`. Must be an accessible HTTPS audio URL.
3. Device-installed Vietnamese browser voice only. Never synthesize Vietnamese using an English voice.
When none is available, no narration is played. Visual game notifications remain.

## Front-end configuration
Add before the main game script:
```html
<script>
window.BUM_AUDIO_CONFIG = { endpoint: 'https://YOUR-OWN-AUDIO-BACKEND.example/api/tts' };
window.BUM_AUDIO_MANIFEST = {
  'proverb-0': 'audio/proverbs/00.mp3',
  'proverb-1': 'audio/proverbs/01.mp3'
  // ... add recordings for proverb-2 through proverb-29
};
</script>
```
**Do not add MP3 paths until the files exist.** Each MP3 should contain the full phrase spoken by the game: "Mỗi ngày một điều hay. [proverb]. [lesson]." Recording indexes match the order in the `sayings` array in `index.html`.

## Backend security requirements
- Store TTS API keys exclusively as backend secrets, never in GitHub Pages HTML or public JS.
- Allow requests only from the game origin; validate origin and text length (max 350 characters).
- Add rate limiting, caching, usage quotas, abuse controls, and timeouts.
- Normalize/sanitize player names; do not log children's names or audio content by default.
- Choose a verified Southern Vietnamese provider voice; do not assume that locale `vi-VN` implies Southern accent.
- Return a short-lived HTTPS audio URL; configure CORS for the game origin where needed.
- Test Chrome/Windows playback, user gesture/autoplay restrictions, repeat entry and rapid notification cancellation.

## Next deployment steps
1. Approve the Southern Vietnamese voice and generate/review 30 prerecorded proverb MP3 files.
2. Upload them under `audio/proverbs/` and populate `BUM_AUDIO_MANIFEST`.
3. Choose/authorize a TTS provider and deploy a backend proxy with secrets.
4. Set `BUM_AUDIO_CONFIG.endpoint`, test dynamic Sao Đỏ names, and audit audio quality.
