# Chainlink

Chainlink is Daniel's nightlife product: a **CRM for bars and clubs plus a Gen Z patron app**. Videos are vertical **1080×1920 at 30 fps** talking-head promos by **Daniel Klabunde** (Partner & CMO: energetic, confident, blunt) and **Devon Leadman** (Founder & CEO: calm, measured tech pro). Full product facts, colours and fonts are in the source brief `chainlink-edit/brand_brief.md` (42 KB). This file holds the editing rules.

Hard rules from the brief: **no made-up stats, no invented app screens, only live features shown as live, IG safe zones clear, no overlapping elements.** CTA idea: *"Every bar already has a Chainlink page. Claim it free at thechainlinkapp.com."* Never use the crypto "Chainlink" (smartcontractkit) name, logo or visuals.

## 1. Cutting talking heads (from the c0140–c0142 build scripts)
- **Hard cuts.** Talking-head joins are straight cuts; no dissolves (`XF=0`). At **segment boundaries** (between script lines) a **J-cut offset** is used; inside a segment, joins are straight cuts.
- **Pause removal** (`build.py`, as implemented):
  - Each segment is trimmed to speech: lead-in kept **40 ms** before the first voiced frame (`PRE=0.04`), tail **45 ms** after the last (`POST=0.045`).
  - Any internal silence **≥ 350 ms** (35 × 10 ms frames below threshold) is cut out, keeping **55 ms** of air on each side (`KEEP=0.055`).
  - Voiced threshold = max(5th percentile + 10 dB, 95th percentile − 35 dB) on a 10 ms RMS envelope.
  - Piece ends are snapped to the 30 fps frame grid (rounded up only if the extension is quiet, otherwise down).
  - Every audio piece gets **8 ms** fade-in/fade-out to avoid clicks.
- **Stutters / false starts / repeated words:** the rule for these is **not written in any saved note** (UNKNOWN). The EDLs were built from word-level transcripts (`words*.json`, `segwords.json`, `wordpool.json`) by choosing clean phrases, which implies "keep the clean take of each phrase, drop false starts". Confirm with Daniel.
- **Script match:** transcribe (Whisper word timestamps), align to the script, pick the best phrase take per line. *c0141 A = Daniel's PAS "guessing event nights" script; B = an unscripted ramble (414–569s) cut into 15 segments.*

## 2. Eye pinning (prevents face jumps between takes)
As implemented in `c0142/path.py`:
- Track the eyes per frame (MediaPipe face landmarker). Per shot, a heavy Gaussian smooth (sigma = min(12, n/4) frames) follows the head inside the shot, blended so the first and last frame match a light smooth (sigma 1.5): the pin is exact at every cut.
- Pin the eye line at **y = 440 px of 1920**, x centred (540).
- Base zoom alternates **1.2× / 1.3×** shot to shot. A slow push of about ±2.25% (`(p−0.5)·0.045`, smoothstep) runs across each shot, alternating direction.
- **Feasibility:** each shot's base scale is bumped up just enough that the pin never exposes a frame edge: `r = max(540/ex, 540/(1080−ex), 440/ey, (1920−440)/(1920−ey))`.
- Daniel's exact wording of the face-jump note isn't on file; this is the implemented rule.

## 3. Zoom, motion and transitions (approved promo v5, Remotion)
- Slow push on every scene (scale **1 → 1.06**).
- Cards/panels: **continuous 3D drift** (rotateX/Y sine wobble, perspective 1700) with frequent direction changes. Never dead still.
- 6-frame transitions, mixed up: **whip-pan** (±1300 px, skew 12°, motion blur 30), **card flip** (rotateY 90°, perspective 2200), **zoom-through** (scale ×5 + blur), drop, hard cut.

## 4. Text
- Reveals take **2–6 frames** at 30 fps (ease-out `cubic-bezier(0.16,1,0.3,1)`, in-out `(0.65,0,0.35,1)`; spring damping 12, stiffness 180, mass 0.7).
- **Full sentences ≥ 1.5 s (45 frames).** One-word hits are very fast, on the beat.
- Rotate styles: cut-on (word by word, 1.14 pop for 2 frames), mask-up (105%→0 over 5 f), punch (1.7→1 over 5 f), blur-in (22 px blur + tracking collapse over 6 f), scramble (Share Tech Mono, ~9 f), type-on with caret.
- Headlines: **Manrope 800**, white, letter-spacing −2, line-height 1.02; sub-text Manrope 700 in muted `#8B85A8`. Data/receipts: Share Tech Mono. Wall captions only: Permanent Marker (≈ −1.4°).
- **CTA lands on the beat drop and appears whole** (never word by word). New videos use the matte orange button (`#FFA064 → #F55400`, lip `#B23C00`, no gloss).
- Devon's videos: `[DEF: term = definition]` cards (≤12 words) at the first spoken mention, held ≥1.5 s, once per term.

## 5. App screens
- **Real screenshots only, matching the current app.** Never invent or mock UI. Current Wall: `app-wall-new.png` (not `app-wall.png`).
- *Wall fix lesson:* the caption was changed from "Ordered a SODA" to **"Ordered a drink"** with a real cocktail photo. Use realistic nightlife content.
- Analytics/patron screens are built but show sample data: label them **"SAMPLE DATA"** (Share Tech Mono 18 px, muted, 1 px border, radius 6). Planned features get **"COMING SOON"** (gold `#FFC46B`).
- Taps and ripples over real screens are fine; changing the UI is not. If a screen doesn't exist, ask for a real screenshot.

## 6. Safe zones (1080×1920)
Keep clear: top ~220 px, bottom ~300 px (the v5 review overlay used a 380 px band with a line at y=1650), right ~120 px in the lower half (from y≈960). Nothing overlaps.

## 7. Speakers, voice and length
| | Daniel | Devon |
|---|---|---|
| Persona | Energetic, blunt, slightly cocky; one contrarian point; punchy closer | Calm, measured, authoritative; no hype; on the owner's side |
| Edit feel | Faster cuts, punchier text hits | Cleaner, calmer pacing; varied but less frantic text |
| Pace | ~209 wpm (voice profile) | **~170 wpm** on camera; scripts ≈160–175 words per 60 s, ≈250 per 90 s; scripted VO reads ran 220–235 wpm (not his natural pace) |
- **Devon pointers:** he doesn't pause to define terms; the DEF card does it. Keep his pacing calm. His advice on bounties: target the guests the badges point to, don't give them to everyone.
- **Length:** reference promos run **25–31 s** (promo v5 31.1 s, data promo v2 24.8 s). Trim to the script; no padding. **Cut slow stat/fact scenes**: if it drags, cut it (data promo lesson).

## 8. Grade and audio on the talking-head A-roll
- Grade: `grade/wb_curves.cube` (white balance + curves) followed by `grade/his_v8.cube`, a LUT fitted from Daniel's own grade of the A-roll (the `g8/` folder holds the fit: paired frames `b_*.png`/`w_*.png`, `fit.py`). Those LUTs are not in this repo; copy them from `chainlink-edit/grade/` if needed.
- Voice chain (`grade/voice_af.txt`, ffmpeg): `highpass=f=70`, `afftdn=nr=10:nf=-48:tn=1`, a 1/3-octave match EQ (−6 dB @20 Hz, −3 @60, +2.3–2.8 dB 160–400 Hz, −1.5 to −1.8 dB 800–1250 Hz, +3.9 to +5.8 dB 5–8 kHz, +4.5 @16 kHz), then `loudnorm=I=-16:TP=-1.5:LRA=9`.
- Music is aligned so the song's end lands at the video's end (`MOFF = song_end − total`), and key hits sync to its beat map.

## 9. Status notes
- c0141: transcripts, EDLs (A 17 segments, B 15) and the pipeline (build → caps → track → path2 → voice af → Remotion render) exist; the Remotion compositions are `Edit141.tsx`/`Edit141b.tsx`. Its last note says the source chunks were still being extracted from Daniel's Linux machine ("pillar"), so the build wasn't finished.
- c0140 went to v8 and c0142 to v5 (files in `chainlink-edit/`). Daniel's specific notes on those versions are **not saved** beyond the rules above.
