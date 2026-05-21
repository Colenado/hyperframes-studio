# Audio

TTS, captions, and music-only workflow.

## TTS (Text-to-Speech)

```bash
npx hyperframes tts "Script text" --voice af_nova --output narration.wav
```

Voices: run `npx hyperframes tts --list`. Voice ID format: `a`=American English, `b`=British English, `e`=Spanish, etc.

## Captions

```bash
npx hyperframes transcribe narration.wav
```

Generates word-level transcript. Caption positioning: landscape → bottom 80-120px centered; portrait → 600-700px from bottom.

### Caption Exit Guarantee

Every caption group MUST have a hard kill after its exit tween — otherwise groups leak into later scenes:

```js
tl.to(groupEl, { opacity: 0, scale: 0.95, duration: 0.12, ease: "power2.in" }, group.end - 0.12);
tl.set(groupEl, { opacity: 0, visibility: "hidden" }, group.end);
```

## Music-Only Workflow (No Voiceover)

When the user says "skip voiceover" / "I'll add music" / "no TTS":

1. **No SCRIPT.md** — timing from content density, not narration
2. **No TTS step** — skip `npx hyperframes tts` and `npx hyperframes transcribe`
3. **No captions** — nothing to transcribe without speech
4. **GSAP entrances drive timing** — use longer holds (2-4s after last animation) so music has room
5. **Leave audio placeholder** in root composition:
   ```html
   <audio data-start="0" data-duration="46" data-volume="0.8"
          data-track-index="10" src=""></audio>
   ```
6. **Prefer single-file composition** for music-only — faster iteration
7. **Draft render first** — visual pacing check only, no audio sync to verify
