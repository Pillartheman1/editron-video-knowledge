# Audio

## 1. Music
- **Where to start:** where the **vocals/lyrics enter**, or at the **main drop** for instrumentals, always on a beat. *Zaidi 5 v1 note: start at the lyrics.* Find vocal entry with demucs two-stem separation (vocals vs no_vocals) plus librosa beat tracking.
  - Zaidi 5: 16.509s (beat 32; vocals enter ~16.55s, full at 17.04s). Zaidi 6: 40.449s (main section drop). Zaidi 7: 26.378s (main section beat). Zaidi 8: 2.252s (vocals), band enters +11.66s.
  - Alternative (Zaidi 3, Chainlink): align the song so its **natural ending lands on the last frame**.
- **Runs to the last frame**, short tail fade only: 0.28s (Z6), 0.4s (Z7, Z8), 0.5s (Z5 v6). *Zaidi 4 "silent tail" note.* On a shortened re-cut, re-render the music continuously at the new length rather than cutting it (Zaidi 5 v6).
- **Sync cuts to beats.** Map reference cut times onto our song's beat grid.

## 2. Loudness
- Zaidi Reels: **loudnorm −14 LUFS integrated, TP −1** (`loudnorm=I=-14:TP=-1`). Measured finals read about −12 LUFS integrated with ebur128 (Zaidi 8 v2/v3 and Daniel's export both −12.0).
- Chainlink voice: `loudnorm=I=-16:TP=-1.5:LRA=9`.
- **Gotcha (Zaidi 4 v4):** loudnorm shifts timestamps by ~85 ms, so a following `atrim` cut off the tail (and each voice piece end). Fix: add **`asetpts=N/SR/TB`** right after loudnorm, then `aresample=48000`. Always check that audio duration = video duration.
- 48 kHz everywhere. demucs stems come out at 44.1 kHz; resample them before mixing.

## 3. SFX
- **Subtle: at most one effect, tied to an action.** *Zaidi 5: "Paper SFX too much."* Final: one paper rustle at **−30 dB** on the flip. Free SFX source used: BigSoundBank (CC0).

## 4. ADR / lip-sync voice swap (phone Reel, v8 → v10)
Job: replace only Leena's (the automated voice's) lines with new takes, leaving the male caller, music and SFX exactly as they were; captions must follow the new wording.

### v8: alignment and replacement
- Identify the voice by pitch (caller ~110 Hz, original automated voice ~290 Hz, new take ~179 Hz) and by the new take's transcript.
- Transcribe the new take with word timestamps (faster-whisper small int8, fed 16 kHz numpy audio).
- Per line, align to the original's start and word timing; time-stretch with pitch preserved (atempo), **clamped to ±8%**. The new delivery was 15–34% slower, so most lines ran at 0.92 and some had to be placed early/late inside the caller's gaps:

| Line | Placed at | Speed | Offset vs original |
|---|---|---|---|
| "For launch party details," | 7.60s | 0.93 | −0.04s |
| "press 1." | 9.00 | 0.957 | 0.00 |
| "Zaidi Jewelers invites you…" | 10.63 | 0.92 | −0.01 (ends ~0.2s late) |
| "November 15, 2026," | 17.69 | 0.92 | 0.00 |
| "from 2pm to 6pm." | 19.89 | 0.92 | +0.29 (moved to fit) |
| "Westwood Vowery & Estate in Westfield, Indiana." | 23.10 | 0.92 | −0.47 (moved into the gap after the caller's "be?") |

- Muting her original without touching the man: inside her regions only, swap the original mix for demucs **no_vocals** + the new layer, with 40 ms crossfades. Outside the regions the original mix is bit-identical.
- Match-EQ FIR (1/3-octave-smoothed Welch spectrum ratio vs her original stem, clamped ±12 dB), compressor, RMS level match per region (+21 to +26 dB), and a peak guard/limiter on the layer only (an early version peaked at 1.29 and clipped).
- Lip sync only mattered where she's on screen (first two lines, ~5–8s); the rest is over B-roll. Without listening, sync was checked with onset envelopes and cross-correlation.
- Caption update: added "in" before "Westfield" and shifted block times.

### v9: "Westwood" dropout and phone voice
- **Bug:** L4 was placed at 23.10s, but the 4th replacement region still started at 23.40s (where the old "Westwood" began). The +26 dB gain only applied inside the region, so "West-" (23.05–23.40) played ~27 dB too quiet, then jumped. **Lesson: apply level gain over the whole placed piece, not over the old region; start the mix swap where the new piece starts.**
- **Phone voice chain on her layer only** (after comp, before level match): HPF **300 Hz 24 dB/oct**, LPF **3400 Hz 24 dB/oct**, **+4 dB @1.5 kHz** (1.3 oct), **−1.5 dB @3 kHz**, acompressor **4:1 at −22 dBFS** (3/60 ms, +6 dB makeup), tanh soft-clip 0.7, acrusher 12-bit log, mix 10%. Mono. Synthetic room wet reduced 0.22 → 0.05.
- Video stream-copied from v8 so captions and grade stay untouched.

### v10: echo removal
- Added reverb removed entirely (wet 0).
- **Dereverb the raw take before stretch/phone.** Tested (word-end T20 decay on 24 events): raw 380 ms; WPE 20 taps 288; DeepFilterNet3 152; DF + post-filter 138; WPE→DF+pf 120; SpeechBrain sepformer-whamr16k 170; sepformer→DF 88. **Sepformer rejected for warble** (per-bin gain jitter 3.6–3.8 dB vs 1.1 for WPE→DF; loud-frame gain s.d. 8–14 dB).
- **Chosen chain:** nara_wpe (taps 20, delay 3, 5 iterations, 1024/256 STFT) → `deep-filter -D --pf` → gentle expander (agate threshold −42 dBFS, ratio 2, range −10 dB, 5/150 ms). Final word-end T20: 300 ms (v9) → **128 ms**.

## 5. Voice layering (Zaidi)
- Her VO under B-roll is fine when the delivery only exists in a weaker visual take (Zaidi 4 L4 tail from C0097). Pull 48 kHz audio from the Mac originals, not proxy audio (Zaidi 4 v2 proxies were 16 kHz flac and had to be re-pulled).

## 6. Chainlink voice
See `clients/chainlink.md` §8: HPF 70, afftdn, match EQ, loudnorm −16. 8 ms fades on every cut piece.
