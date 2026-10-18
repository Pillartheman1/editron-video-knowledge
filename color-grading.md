# Colour grading

> **Read this first.** Daniel grades the final Reels himself. Everything below was **measured from pixels**: his exported graded file was frame-aligned with the ungraded render and with our version, then compared (luma percentiles, CIELAB, chroma by hue, vignette ratio, detail bands), and a 33³ LUT was fitted from ungraded to his. These are **estimates of the look, not his actual settings.** Once Build has access to his real **DaVinci Resolve node trees / .drp projects / PowerGrades**, learn from those and treat them as the source of truth; use the numbers here only to sanity-check.

## Quick rules (which look to use)
| Footage | LUT (`luts/`) | What it does |
|---|---|---|
| **Indoor soft light / interiors** (Zaidi 8) | `daniel_zaidi8.cube` at 100% | About −0.25 stop, near-black blacks, clean neutral whites, warm rosy skin, yellow slightly desaturated, ~17% vignette, mild softening |
| **Shade, dusk, flat exteriors** (Zaidi 5) | `daniel_zaidi5.cube` at 100% | About +1 stop, warm mids, neutral cream highlights, no vignette. Dusk: lift highlights, keep shadows moody |
| **Sunlit, contrasty, flare, car** (Zaidi 7) | `daniel_zaidi7.cube` at 100% | No brightening, near-black shadows, warm golden highlights pulled to ~0.85–0.9, ~25–28% vignette |

- **Mids follow the footage: ~0.25–0.45 luma.** Lift flat shade footage, leave sunlit footage alone, pull already-exposed interiors down slightly for contrast.
- **Always:** blacks never lifted (often crushed); highlights rolled off, never hard-clipped; warm or rosy skin; gold rich and orange-leaning; no blue or green cast; no grain, no sharpening; one look across the whole Reel with small per-shot exposure trims.
- **Whites** (porcelain bowl, paper, white dress): remove yellow from the highlights only. **Never cool the whole image.**
- **Regrade conservatively when unsure.**

### LUT input convention
All three LUTs expect **ungraded Rec.709 footage as full-range RGB** (0–1) and output full-range RGB. In ffmpeg: `scale=in_color_matrix=bt709:in_range=tv:out_range=full,format=rgb24,lut3d=FILE.cube`, then convert back to tv-range bt709 YUV. In Resolve, apply on a node working in Rec.709 (see `resolve-notes.md`). Fit error (mean ΔE2000 vs his export): #5 2.2, #7 1.7, #8 1.4. The #8 LUT's neutral axis was made monotone where the footage had no data (pure greys are rare in warm footage), so extreme inputs are extrapolated.

## Our grading mistakes (what was rejected and why)
| Version | What we did | Outcome / lesson |
|---|---|---|
| Zaidi 4 v4 | Heavy outdoor regrade | Called **"abysmal"**. v5 regraded R1 from the ungraded source, conservative: gentle curve, slight warm, sat 1.06, bt709 full-range RGB path. Lesson: regrade conservatively. |
| Zaidi 5 v5 (ours) | "Light natural" grade | Daniel's own grade was ~1 stop brighter, warmer and richer. Lesson: ours was too timid on shade footage. |
| Zaidi 6 | Pre-curve mid lift, Zaidi 5 LUT at 65%, rolloff to 0.96 | Shipped (no grade note). |
| Zaidi 7 v1/v2 (ours) | Mid pre-lift + Zaidi 5 LUT at 62% + rolloff 0.94 on sun footage | Came out **milky**: median luma 0.54 vs his 0.39. ΔE to his 12.0, worse than ungraded (11.4). Lesson: don't run the shade LUT plus a lift on bright footage. |
| Zaidi 8 v1 | Zaidi 5 LUT at 100/75/55% by shot brightness, rolloff 0.94 | Daniel: **"too warm and gold, the bowl blown out"**; asked for a low LUT mix or a neutral grade. |
| Zaidi 8 v2/v3 | Neutral: channel mixer R 0.94 / G 0.985 / B 1.10, Zaidi 5 LUT at 15%, curve (0.25→0.28, 0.6→0.64, 0.85→0.84, 1→0.93), saturation 0.88 | Daniel then graded v3 himself. Ours was **further from his than the ungraded file** (ΔE 8.9 vs 6.2): we lifted (+0.2 stop), cooled globally (slightly green, lost rosy skin) and desaturated everything, the opposite of his moves. |

## Full measured notes (verbatim from `zaidi-grade-notes.md`)

# Zaidi grade notes: Daniel's own grade of zaidi5_v5 (Oct 15 2026)

Source: grade/daniel_v5.mp4, exported 4K 10-bit bt709 and frame-aligned to v5 at offset 0 with the same framing.
LUT: /workspace/zaidi5/grade/daniel_zaidi5.cube (33³). Input is ungraded Rec709 footage, full-range RGB.
- ffmpeg: `scale=in_color_matrix=bt709:in_range=tv:out_range=full,format=rgb24,lut3d=daniel_zaidi5.cube`, then convert back to tv-range yuv.
- Fit error: mean ΔE2000 12.6 (ungraded vs his) → 2.2 (LUT vs his).

## The look in plain words
His grade is much brighter, warmer and richer than our "light natural" grade. Our grade was too timid.
1. **Exposure:** midtones go up about one stop (mean L* 30→38; e.g. the opener's median luma 0.29→0.44).
   - Neutral curve: 0.2→0.22, 0.3→0.40, 0.5→0.65, 0.7→0.85, 0.8→0.95.
   - The highlights roll off softly near 0.97. Whites stay soft and never clip hard.
2. **Black and white points:** blacks stay at or slightly below the original level, so there is no lifted or faded look. The white point is pushed up to ~0.95 (p99 0.68→0.92). The result is a bright image with real contrast.
3. **White balance:** strongly warm.
   - He removes the cool or blue cast completely: neutrals go from b* −1 to about +8 to +12 in the shadows and mids, and he adds a little magenta (a* +4).
   - Highlights stay almost neutral (only b* +3), so whites read clean cream, not yellow.
   - This works like a split tone: warm shadows and mids, neutral highlights.
4. **Saturation:** selective.
   - Skin and warm tones go up ~2.4x in chroma, with hue nudged toward orange/golden. Gold and yellow go up ~2.2x with the hue held.
   - Reds and magentas go up ~2.3x and shift toward orange (lips and brick). Greens go up ~1.9x and lean yellow.
   - Blue/violet casts drop to 0.4x, so paper, white dress and pavement lose their blue.
5. **Per shot:** he used one look on every shot, with small exposure trims.
   - The opener (shot 1, knees-down walk) is brighter than the LUT predicts (ΔE 5.7 vs ~2 elsewhere).
   - The dusk car-window ending keeps dark mids. He brightened only the highlights there and did not lift the shadows.
6. **No vignette** (the brightness ratio is flat from center to edge). **No added sharpening and no grain**: high-frequency detail is unchanged or slightly softer, and noise is unchanged.

## To reproduce on a new Zaidi edit
- Apply daniel_zaidi5.cube to ungraded clips, then trim exposure per shot.
  - Shade and dusk footage like zaidi5: use it at 100%.
  - Already-bright indoor or window footage (zaidi4 Westwood test): pull exposure down first or mix the LUT at ~60%, or windows clip and greens get loud.
- Keep the dusk shots moody: brighten the highlights, not the shadows.
- Check that skin reads warm and golden, gold pops, and whites look clean cream with no blue.
- Don't add a vignette, sharpening or grain.

Files: grade/sidebyside_4shots.jpg (ours vs his, shots 1, 7, 14, 16), grade/lut_test_zaidi4.jpg (R1, R3, R5, R7, before | LUT).

---
# Zaidi 7: Daniel's grade of sunlit car / flare footage (Oct 16 2026)
Source: zaidi7/grade/daniel_v2.mp4 (4K 10-bit, 330 frames). Same frames, edit, crop and audio as our zaidi7_v2; nothing re-timed or reframed.
LUT: /workspace/zaidi7/grade/daniel_zaidi7.cube (33³; same input convention as the Zaidi 5 LUT). ΔE vs his: ungraded 11.4, ours 12.0, Zaidi 5 LUT at 100% 10.0, Zaidi 7 LUT 1.7.

## What he did on bright sunlit footage
1. **No overall brightening.** Median luma stays put (0.385→0.39); ours went to 0.54, which was too bright and milky. Curve: 0.1→0.03, 0.25→0.21, 0.5→0.51, 0.75→0.73, 0.9→0.81, 1.0→~0.85.
2. **Deep blacks.** p10 0.065→0.007. The shadows (wheel, dash, sleeve) go near black, which gives punchy contrast and a moodier, filmic look.
3. **White point pulled down to about 0.85–0.9 and tinted warm.** Blown sky and sun become warm cream rather than white. Only 0.2% of pixels clip; flares keep their colour (purple and green) and glow.
4. **Very warm everywhere, highlights included.** Neutrals b*: shadows +3, mids +16, highlights +11; a* +3 to +8 (slightly rosy). Zaidi 5 kept the highlights neutral; here the whites go golden.
5. **Saturation:** skin about 2.5x (golden-orange), gold about 2.9x, reds about 2.7x. Blues/greys are pushed toward warm (the grey dash goes neutral-brown).
6. **Vignette added:** edges about 25–30% darker (brightness ratio 1.09 center → 0.72 edge). Zaidi 5 had none.
7. No grain and no sharpening; detail is about 8% softer, which is export resampling. Same as Zaidi 5.

## Consistent across Zaidi 5 and 7
Warm golden skin, no blue cast, strong saturation on skin, gold and lips, blacks not lifted, highlights rolled off rather than clipped, no grain or sharpening.

## Different, and depends on the footage
- **Exposure follows the footage.** Shade or dusk (Zaidi 5): about +1 stop. Already-bright sun (Zaidi 7): zero lift, crushed blacks and a lower white point. Rule: aim mids at ~0.35–0.45 luma, wherever they start.
- **Highlights:** neutral cream on soft light (Zaidi 5); warm golden on sun-blown footage (Zaidi 7).
- **Vignette:** used on the sunlit car close-up (to pull the eye to the hand and rings), not on Zaidi 5.

## Which LUT to use
- Sunlit, contrasty or flare footage: daniel_zaidi7.cube at 100%.
- Shade, dusk or flat footage: daniel_zaidi5.cube.
- Don't run the Zaidi 5 LUT on bright footage plus a mid lift; that is what made our v2 milky.

---
# Zaidi 8 (RH-3): Daniel's grade of indoor soft-light footage (Oct 17 2026)
Source: zaidi8/grade/daniel_v3.mp4 (brief attachment 01M55S2H5VTM33KYX337HNT1GN.mp4; the hash filename in the brief didn't exist). HEVC 10-bit 2160x3840, 603 frames. It matches our zaidi8_v3 frame for frame (offset 0, per-frame luma correlation ≥0.989, no reframe or shift). Edit, cuts and audio are unchanged (audio lag 0, −12.0 LUFS on both; his export has 0.04s more audio tail).
Ungraded reference: grade/ungraded_v3.mp4 (render3 path without the grade). LUT: /workspace/zaidi8/grade/daniel_zaidi8.cube (33³, same input convention as the Zaidi 5/7 LUTs). ΔE2000 vs his: ungraded 6.2, our v3 8.9 (further than doing nothing), Zaidi 5 LUT 15.2, Zaidi 7 LUT 8.7, Zaidi 8 LUT 1.4.
Side-by-side: zaidi8/grade/ungraded_ours_his.jpg (walk-in, overhead bowl, lying + bowl).

## What he did
1. **Darker, not brighter.** Median luma 0.32→0.27, about −0.25 stop; every shot drops ~0.04–0.05 with one look throughout. Ours went up to 0.36 (+0.2 stop), the wrong direction. Curve: 0.07→0.03, 0.17→0.12, 0.28→0.23, 0.5→0.46, 0.72→0.72, 0.88→0.84, 0.93→0.88. Lowered shadows and lower mids; the upper mids hold.
2. **Deep blacks.** p1 0.032→0.004, p10 0.072→0.026; the black couch and side table go near black. Same move as Zaidi 7.
3. **White point ~0.88–0.92, soft roll-off.** p99 0.83→0.82. The bowl's brightest spots keep the same brightness (top-2% luma 0.78→0.79) and only ~0.1% of pixels clip on the specular rim. He didn't tame the bowl highlights with exposure; he cleaned their colour.
4. **White balance: the bowl goes clean white, not cream.** Bowl highlights go from b* +27 (yellow) to +10.5 with a* +3.5→+6.5 (a touch rosy). Neutral highlights go from b* +14 to −1 (neutral to faintly cool). Shadows and mids stay warm and pick up magenta (mids a* +2→+5, b* ~+11). This is the Zaidi 5 split: warm, rosy body and clean whites. Ours removed the yellow by globally cooling (a* went negative, slightly green), and lost the rosy skin.
5. **Saturation:** overall chroma held (17.5→17.4). Skin/orange UP to 1.2x (21.8→25.9) with hue shifted 7° toward red/rosy. Yellow/gold DOWN a little (19.5→17.2, 0.88x), with hue shifted 13° toward orange: the beige floor and walls lose their yellow and the gold rim reads richer rather than lemon. Ours desaturated everything (0.88) and pushed hues toward yellow-green, the opposite of his.
6. **Vignette:** yes, moderate. Brightness ratio vs ungraded is 0.97 at center → 0.80 at edge, so edges are ~17% darker. Lighter than Zaidi 7 (~28%).
7. **Softening/denoise:** fine detail (σ<1px band) is 21% lower while the mid band is unchanged, and flat-area noise is 26% lower. That's a mild denoise or softening (more than the 8% resample loss on Zaidi 7); possibly partly the HEVC export. No grain, no sharpening.
8. No edit, length, crop or audio changes.

## Consistent across Zaidi 5, 7 and 8
- Warm, rosy skin; never green or blue in mids/shadows. Gold rich, leaning orange rather than lemon.
- Blacks are never lifted; on 7 and 8 they're crushed to near black. Highlights are rolled off, never hard-clipped.
- No grain and no sharpening.
- One look across the whole Reel; small per-shot trims at most.

## Indoor soft-light rule (Zaidi 8)
- **Already-exposed indoor footage (mids ~0.3) gets darker and more contrasty, not lifted.** The "+1 stop" from Zaidi 5 was for flat shade footage that started at ~0.2–0.29. Revised mid target: 0.25–0.45 depending on mood. Indoor soft light sits ~0.27 (moody, editorial).
- Whites are clean and neutral (porcelain, paper): take the yellow out of the highlights only. Keep the warmth and add a touch of magenta in skin and mids. Never cool the whole image.
- Pull yellow saturation down slightly and lift skin saturation.
- Add a soft vignette (~15–20%) and a mild softening.
- Use daniel_zaidi8.cube at 100% on indoor, tungsten/window-lit interior footage.

## Which LUT to use (updated)
- Indoor soft light / interiors: daniel_zaidi8.cube.
- Sunlit, contrasty or flare: daniel_zaidi7.cube.
- Flat shade or dusk exteriors: daniel_zaidi5.cube.
- Check mids after the LUT and trim exposure per shot. Our neutral "channel mixer + 15% LUT5 + lift" was the wrong approach (ΔE 8.9, worse than ungraded).

## Comparison stills (on Daniel's box, not in this repo)
- Zaidi 5: `/workspace/zaidi5/grade/sidebyside_4shots.jpg`, `lut_test_zaidi4.jpg`
- Zaidi 7: `/workspace/zaidi7/grade/ungraded_ours_his.jpg`
- Zaidi 8: `/workspace/zaidi8/grade/ungraded_ours_his.jpg`, `zaidi8_v2_ref_vs_ours.jpg`

## Other grading techniques used
- Grade chains work in **bt709 full-range RGB** (`gbrp`), then return to tv-range yuv420p (8-bit H.264 for 1080 deliverables, 10-bit HEVC for 4K masters).
- LUT strength via blend: `split[a][b];[b]lut3d=…[c];[a][c]blend=all_expr='A*(1-m)+B*m'`.
- Highlight rolloff curve, e.g. `curves=all='0/0 0.75/0.75 0.9/0.86 1/0.94'`.
- Zaidi 3: Daniel's own graded B-roll was used **untouched**; Leena's A-roll got "the Zaidi look" with a per-shot exposure trim (−0.75 to −0.99 stop) and a light temporal denoise.
- Delivering on top of his grade (Zaidi 5 v6, Zaidi 8 v4/v5): never re-grade his file; verify the untouched sections match his frames (PSNR ≥ ~44–50 dB, or max diff ≤0.27/255).
