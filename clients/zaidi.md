# Zaidi Jewelers

A jewelry store's launch campaign ("Zaidi Jewelers", launch party **November 15, 2026, 2–6 PM, Westwood Vowery & Estate, Westfield, Indiana**). The on-camera talent is **Leena**. Deliverables are vertical Instagram Reels. Footage: Sony clips `C0xxx.MP4`, 4K, on the T7 Shield under `Client footage/Zaidi/Zaidi 1|2/…`. Many clips are recorded **sideways** and must be rotated (ffmpeg `transpose=2`, i.e. 90° counter-clockwise). Frame rate **29.97** (30000/1001).

## 1. Brand (from `zaidi/brand.md`, sourced from zaidijewelers.com theme CSS)
| Item | Value |
|---|---|
| Body/heading font | **Poppins** (weights 100–900 + italics) |
| Sub-heading font | **Libre Baskerville** (400, 400 italic, 700) |
| Background cream | `#FBF9F4` (251 249 244) |
| Foreground | `#1A1A17` |
| Accent deep green | `#0A2422` (10 36 34) |
| Brand gold (site) | `#D0A648`; caption gold gradient `#E6C67A → #D0A648 → #B08838` |
| Others seen | `#222222` text, `#878787` secondary, `#DDDDDD` borders |
| Logo | Only real source found: `Zaidi-Logo-1….png` from zaidijewelers.com, **280×150 px, single-colour gold with alpha** (star mark + "zaidi" + "Jewelers"). Never redraw or invent it. Ask the client for a vector or large PNG. |

The fonts on file are Shopify CDN `.woff2` copies with stripped name tables; names were re-added in the `.ttf` versions so libass can match them.

## 2. Script and A-roll
- **Follow the script order.** Scripts come from Google Keep / Notion.
- **Real delivery takes only**, to camera or at a steady documentary off-camera eyeline. *Zaidi 4: C0095 at 238.35s was a phone-reading take and was replaced with C0096 ~53.9s (WESTWOOD wall, to camera).* The audit measured loudness, pace and gaze (MediaPipe blendshapes) per take.
- **Layer her VO under B-roll** where the script line only exists in a weak take. *Zaidi 4 v6: the L4 tail "and find pieces that feel right for you" came from C0097 33.48–35.75s (48 kHz audio pulled from the Mac) under shot Z4.6, and the title card it replaced was removed. Daniel asked for her audio instead of a title card.*
- **Moving-camera A-roll on the emotional line:** "So come celebrate with us" = the R6 orbit shot (light grade, unstabilised).
- **No dead air.** *Zaidi 4: a 1.5s silent title card at 0:26 felt like "a long gap at 26s from end"; it was trimmed to 1.0s and later removed.* No speech pause ≥0.25s was left anywhere.
- **Shaky A-roll that can't be fixed → cover it with steady B-roll.** *Zaidi 4 v4: background-only jitter R1@0.0 12.4 px, R2@9.84 9.2, R6@39.48 22.1, R6@43.9 21.1. vidstab plus a custom background-masked stabiliser only reduced it 10–20%, so steady B-roll was laid over instead.*

## 3. Shotlists, "misc" and reference matching
- **Follow the Notion shotlist order** and **match the reference Reel shot for shot**, with a match table (ref shot → our clip/time), then adapt the pace to Daniel's notes.
- The Notion row for a Reel can be thin. *Zaidi 8 (RH-3): only "props: bowl, book; shoot at West (couch + table); wearing lots of jewelry", with no shot rows and no misc rows*, so the reference became the shot plan.
- **"misc" rows:** the brief for this repo mentions shotlists including "misc" rows, but no saved notes describe what misc rows contain. **UNKNOWN:** treat misc rows as extra shots Daniel wants included, and ask if unsure.
- **The reference's audio differs from our song.** Map the reference's cut points onto our song's beats (Zaidi 5: ref beat 0.74s ↦ song beat 1.88s; ref tempo ~107.7 BPM). Zaidi 8: reference cuts were snapped to beats with a 0.5s minimum.
- Check whether the reference is truly one shot. *Zaidi 7's reference (DZsIA8Lkibn, 19.16s, 25 fps) hard-cuts at 12.32s to a rainy-windshield end card with "FILL LIFE WITH LOVE" and the "Lunn's Portfolio" logo.*
- Reference text is copied for **words, line breaks and timing**, restyled in Zaidi's caption style. **Never put another brand's name on screen** (Zaidi 8: "TROIS MONCEAU" was left out; the Zaidi logo went in that slot).

## 4. Single-shot Reels (Zaidi 6 and 7)
- **Match the reference length in exact frames at 29.97.** Zaidi 6: 308 frames = 10.277s for a 10.29s reference. Zaidi 7 v1: 574 frames = 19.152s for 19.16s.
- **Pick the steadiest continuous window that matches the reference action**, analysing shake, action and light over the whole clip. *Zaidi 6: C0103 source 2.00–12.28s, background jitter ~0.2 px/frame, no stabilisation.*
- **Tight tracked crop from 4K on hands and rings; never upscale.**
  - *Zaidi 6 v1 was rejected* ("crop in so her hands fill the frame like the reference"). v2 used a **1.8x** crop (a 1200×2133 window of the 2160×3840 frame, scaled down to 1080×1920). The track is the skin-mask median centroid, Gaussian-smoothed (sigma 12 frames), subpixel translation only, no rotation or warping; the fastest pan is ~24 output px/frame.
  - *Zaidi 7:* **1.25x** tracked crop (1728×3072 window), skin-centroid track, sigma 18.
- **No angle-finding.** *Zaidi 7 v1 included 3.4–6.6s (source 53.4–56.6s), a camera reposition through a sun wash. Daniel: skip the first part, "I was still finding the angle." v2 kept only the last 330 frames (source 58.09–69.09s), 11.011s, shorter than the reference.* No 19s window in that clip had both flares and no reposition.
- **Flares**: wanted (Zaidi 7 brief: "one or two light flares, rings prominent, steady shot"), but only on a steady frame with readable rings.
- 4K pulls come from the Mac. CopyToBox is capped at 100 MiB, so split or tar bundles under that.

## 5. Pacing
- **Hard cuts on beats, no flash frames;** no shot under ~2 beats (Zaidi 8 allowed 0.5s minimum for overhead drops, per Daniel's note).
- **Average ~1.0–1.2s per shot** for montage Reels, on whole beats of 2–4. *History: Zaidi 5 v1 "choppy" (25 shots in 17.8s) → v2 too slow (18 shots, 26.29s, avg 1.46s; Daniel: "tighten pace") → v3 20 shots avg 1.11s → v4 16 shots avg 1.17s → v5 approved.*
- **Fewer, stronger shots; one idea per shot** (see shot-selection rule 13).
- Zaidi 8 (20.12s, 603 frames): cuts at frames 87 132 166 200 234 267 309 340 372 403 454 485 506 537, all beat frames; min shot 0.67s.

## 6. Music
- **Start the music where the vocals or lyrics enter, or at the main drop, on a beat.** *Zaidi 5 v1 feedback: start at the lyrics.* Examples:
  - Zaidi 5: song from 16.509s (beat 32; demucs vocals enter ~16.55, full at 17.04).
  - Zaidi 6 (instrumental): from the beat at 40.449s, where the main section drops.
  - Zaidi 7 ("Anaheim", By Lotus, instrumental): from the main-section beat at 26.378s.
  - Zaidi 8 ("I Need You Darlin'", baegel): vocals at 2.252s, so the music starts there; the band enters at +11.66s.
  - Zaidi 3: "guidance" (dreem) runs 139.37–197.55s so its natural ending lands on the last frame.
- **Music runs to the last frame** with only a short tail fade (0.28–0.5s). *"Silent tail" was flagged on Zaidi 4.*
- Loudness −14 LUFS integrated (see `audio.md`).

## 7. SFX
- **Subtle, at most one paper rustle, on the flip.** *Zaidi 5: "Paper SFX too much." v3 onward used one rustle at −30 dB, on the flip.* Zaidi 5 v1 had music plus 2 city beds plus spot SFX (BigSoundBank CC0) and was cut back.

## 8. Captions, titles and logo
Approved sources: `zaidi2/build8/cap.py`, `zaidi/v7.py` (phone Reel), `zaidi/make_subs.py` + `phone_captions.ass`, `zaidi4/build3/title.py`. Sizes below are on a **2160×3840** canvas (halve for 1080×1920).

### Roles
| Role | Font | Size | Colour | Tracking | Notes |
|---|---|---|---|---|---|
| **B** (emphasis/headline word) | Libre Baskerville Bold (`librebaskerville_n7`) | 300 px × per-word scale (0.8–1.25 used); shrink until the line ≤1760 px | Vertical gold gradient over the ink box: top `#E6C67A` (230,198,122) → mid `#D0A648` (208,166,72) → bottom `#B08838` (176,136,56) | 0 | e.g. "November" 0.8, "15th" 1.25, "Vowery" 0.8 |
| **S** (small connector) | Poppins SemiBold (`poppins_n6`) | 104 px | Cream `#FBF9F4` | 14 | UPPERCASE: "about", "from", "in", "& Estate" |
| **M** (plain line) | Poppins Medium (`poppins_n5`) | 124 px (auto-shrink to fit 1800 px) | Cream | 2 | Most dialogue lines |
| **Accent / voice** | Libre Baskerville Italic | — | Cream | — | Phone Reel automated-voice lines (ASS style `voice`, 184 px); used for script words like "Boring" |
| Phone Reel caller (ASS) | Poppins SemiBold | 176 px | Cream | 2 | Outline 9, shadow 8, colour `#0A2422` |

### Shadow, layout, animation
- **Shadow:** soft only, no outline, **never a box.** v7: black Gaussian blur radius 30 at 35% opacity, offset 6 px down (zaidi2 used 60%). Brand notes allow `#0A2422` as the shadow tint (the #6 overlay used it).
- **Stacking:** centre each line on its **ink** box, not the advance box. Gaps: B–B 6 px, between different roles 30 px, M–M 22 px. Stacked titles (Zaidi 8 v5): an equal ink gap of ~0.36 cap height and a constant baseline pitch.
- **Animation:** B pops over 0.12 s (scale 1.08→1.0, ease-out cubic, with a fade); S and M appear on the cut; blocks cut out hard at their end time. ASS version: `\fad(90,0)` with a 0.92→1.0 scale over 120 ms; inactive words dimmed (`\alpha&H48&`).
- **Placement:** phone Reel captions live in a band starting at y=1300 with the block bottom at y≈2900 (75.5% of 3840). Always stay within **10–80% of the height**, away from the right-side IG buttons, clear of face, hands and jewelry; check with a pixel-overlap test.
- **Captions must match the spoken words exactly**; if a line's wording changes (ADR), update the caption (phone Reel v8 added "in" before "Westfield").

### Examples
- **Zaidi 6 v3** ("COMING SOON / An exclusive event. Get ready for an evening of timeless elegance"): headline Libre Baskerville 78 px (1080 canvas), tracking 10, subline Libre Baskerville Italic 36 px, cream with a `#0A2422` soft shadow; y≈212–480 px of 1920 (11–25%); headline fade+18 px rise 0.40–1.20s, subline 1.30–2.10s, held, faded out 6.20–6.60s because the hands rise into that area from ~6.7s.
- **Zaidi 8 v4 → v5** (reference title "A SERIE / NOT / THAT *Boring* / JEWELRY"):
  - v4 used cream text and kept "A SERIE". **Daniel's v5 notes:** drop "A SERIE" and its underline; even, optically balanced line spacing; consistent tracking; "Boring" sitting naturally beside THAT with a proper gap; truly centred; "use the caption style guide… match fonts, weights, sizes, colours, shadow and animation exactly rather than inventing a new style".
  - v5: NOT/THAT/JEWELRY as **B** (gold gradient Libre Baskerville Bold, 136 px on 4K so the block fits between the bowl and the 80% line), tracking 8 px, "Boring" cream Libre Baskerville Italic at 0.88× size, baseline-aligned with THAT, 29 px gap. Ink gaps 36/37 px, baseline pitch 144 px, line centres 1079.5/1080/1079 px. Timing copied from the reference: NOT 0.667s, THAT *Boring* 1.25s, JEWELRY 1.792s, all out at 4.0s. Position y 2642–3071 (69–80%) on the black side table, below the bowl (bowl bottom ≤2612).
  - Logo v5: Daniel asked for it centred and 1.3–1.5x bigger, upscaled cleanly. 840 px wide on 4K (1.4x of v4's 600), stepped Lanczos plus an alpha edge-contrast curve, cream tint, same soft shadow, fade in 0.5s at 17.45s (reference timing), held to the end. Centred on its ink box (the PNG has uneven padding). y 2660–3066 on the dark couch, clear of her legs.

## 9. Grade
See `color-grading.md`. Short version: Daniel grades the finals himself. Use his LUT for the footage type (#5 shade, #7 sun, #8 interior) at 100%, then trim exposure per shot.

## 10. Project log (what each Reel was)
| Reel | Content | Final |
|---|---|---|
| Zaidi 2 | Leena's launch announcement, A-roll + B-roll, captions (cap.py roles) | v8 |
| Zaidi 3 | Q&A cards + Leena answers, 58.19s (1744 fr) | v1 shot list |
| Zaidi 4 | Westwood venue Reel, A-roll + B-roll, 49.68s | v6 |
| Zaidi 5 | Newspaper "launch event" story, street/car, 25-shot reference | v5 graded by Daniel; v6_graded = 1.2s cut from the walk-away |
| Zaidi 6 | Single shot: hands with a gold ribbon, 10.28s | v2 picture, v3 adds "COMING SOON" text |
| Zaidi 7 | Single shot: hand + rings on a steering wheel in sun with flares, 11.01s | v2 (graded by Daniel) |
| Zaidi 8 (RH-3) | Removing jewelry into a bowl, lying down reading, 20.12s | v3 graded by Daniel; v5_graded adds title + logo |
| Phone Reel | Phone-call launch announcement (caller + automated voice), captions | v7 captions; ADR v8→v10 |

## 11. Oct 18 2026: RH-4 and music lessons
*Promoted Oct 18 2026 at Daniel's request ("learn from everything we worked on today"). Measurements stay in `LEARNINGS.md`; where a rule cites a section (e.g. L§13) that log is the evidence and wins on numbers.*

### RH-4 (narrative Reel, "Cake Bake" café → city centre / Palladium, props cup/books/newspaper)
- **Daniel's approved structure** (L§11/§13; reproduced as `/workspace/zaidi_rh4/rh4_v2.mp4`, notes in `/workspace/zaidi_rh4/EDIT_NOTES.md`): seated reach (2.30 s) → lid off (rings) → stir (punch-in 1.09) → end of sip (full) → overhead empty cup → reading → lift next magazine → page lands on jewelry → tight sip with bracelet (1.29) → square drink → lantern walk → empty street → arm+bracelet+cup at fountain → fountain portrait (0.79) → sits down → bench punch-in (1.10→1.02, 2.30 s) → zaidi card. 751 frames, 25.06 s.
- **What v1 got wrong and the fix:** v1 copied the reference montage beat-for-beat (back-view walk opener, isolated product inserts, tight face/chest/chin details, wide bench ending, cuts on reference times). Daniel's version: open mid-action, the cup carries the story, jewelry only on an acting hand, cut when the action completes, slow zoom ramps, split the sip wide→tight. See shot-selection.md §3 rules 18–26.
- **Reference matching, revised:** match the reference's *feel and story beats*, not its shot list, when our footage tells a better story with the props. "Match the reference shot for shot" (§3) still applies to single-shot and title-copy jobs.
- **End card:** the real zaidi / Jewelers logo centred on black, 480 px wide at 1080 (from the 280 px site PNG, upscaled), zoom 1.28→1.16 over 57 frames, fades to black from card frame 20 over 37 frames, music fades with it. Never another brand's name (LOHR) on screen.
- **Grade:** his tree (curves → wheels → Glow → Vignette), two presets: Look A for hands/table, Look B for faces and exteriors (brighter, warm, third channel pulled = less green). Street plate: smaller opposite hue bow, default glow. Not a cool grade. Numbers in L§13.
- **Sound:** song only. No Foley (rejected, L§12).

### Music
- The client replaced many of Daniel's warm chill / deep-house picks on the posted Reels. **Choose from what was actually posted.** Instagram hides licensed audio when logged out, so ask Daniel for the track names. Candidate analysis: `/workspace/zaidi-music/an.py` (BPM, key, LUFS, energy jump). Posted Reels downloaded in `/workspace/zaidi-music/client/` (Oct 1–7 2026).
