# Shot selection (the most important file)

Every rule below comes from a note Daniel actually gave on a delivered version. Format: **his note (project)** → **general rule**. Apply them to every candidate shot, then run the checklist at the end.

## 1. Every note Daniel gave, and the rule it implies

| # | His note (project) | General rule |
|---|---|---|
| 1 | A-roll line was a **rehearsal / phone-reading take**; use the real delivery (Zaidi 4: C0095 238s → C0096 53.9s) | **Only use real delivery takes, never rehearsals.** A real take is confident, loud and steady in its gaze, whether to the lens or (in documentary-style shoots) at a consistent off-camera angle. Rehearsal signs: glancing down at a phone, a hesitant or quiet read, a drifting eyeline. Audit every take before choosing. The last take is not always the good one. |
| 2 | **Wobbly B-roll** ("wobbly shot 42s from end"); outdoor walk-and-talk shake (Zaidi 4) | **Measure shake against the background, not the subject.** Swap any shot with visible jitter. Stabilising only fixed 10–20%, so replace the shot instead of rescuing it. |
| 3 | **Chandelier shot**: replace it (Zaidi 4 v4) | **If Daniel dislikes a subject, remove every shot of it**, not just the one he named (all chandelier shots went). Prefer shots that show the jewelry, her, or the store's character. |
| 4 | **The opener should have her in it**; something always happening (Zaidi 5 v2) | **Open on the person, in motion.** No empty establishing shot first. Every shot needs visible action. |
| 5 | **End on her walking away, seen through the car window** (Zaidi 5 v2) | **End on a human, story-closing moment** (exit, look-back), not a product insert. |
| 6 | **Keep the hand-in-hair portrait, but not as the last shot** (Zaidi 5 v2) | **A great shot is not automatically the ending.** Put your best stills mid-edit; the end belongs to the strongest motion beat. |
| 7 | **Last shot should include her turn and look-back; start it earlier / land just after the look-back, keep ~3s** (Zaidi 5 v3–v4) | **Trim around the key action.** Start early enough to show the build-up; cut just after the payoff so it doesn't trail off. |
| 8 | **Cut shots that show the real newspaper; swap in the rapid flip, cropped to hide the real paper; flag other real-paper shots** (Zaidi 5 v4) | **Check every frame for props that break the illusion** (the real paper behind the fake sheet, logos, crew, gear). Crop or replace, and proactively flag remaining risky shots. |
| 9 | **Crop the LAUNCH EVENT shot to the fake sheet** (Zaidi 5 v3) | **Crop from 4K to show only the intended prop/message.** |
| 10 | **No repeated shots** (standing preference) | **Never reuse a B-roll clip or near-identical angle** in one edit, even as filler. Track which source ranges are used. |
| 11 | **Walking-past-car shot as a tight knees-down crop** (Zaidi 5 v2) | **Use tracked 4K crops to turn a dull wide into a stylish detail** (legs, heels, hands). Crop from 4K so it stays sharp. |
| 12 | **Swap the shaky feet shot** (Zaidi 5 v3) | **Detail shots are judged for shake too**; tight framing magnifies wobble. Choose the steadiest in-point or another take. |
| 13 | **Delete the ~15s and ~16s shots; cut shot 2; one over-the-paper shot then the wide** (Zaidi 5 v3) | **One idea per shot.** Don't run two similar angles back to back; pick the best and move on. Fewer, stronger shots. |
| 14 | **Crop in so her hands fill the frame like the reference** (Zaidi 6 v2) | **Match the reference framing, not just the action.** Product (hands/rings) should fill most of the frame. Use a smooth tracked crop from 4K: translation only, no warping, smoothed over ~12–18 frames. |
| 15 | **Skip the first part of C0110; "I was still finding the angle"** (Zaidi 7 v2) | **Never include the operator searching for framing** (reposition, swing, refocus). Trim to where the shot is locked, even if it's shorter than the reference. Quality beats matching length. |
| 16 | **Include one or two light flares, keep the rings prominent, steady shot** (Zaidi 7 brief) | **Flares are a feature, but only on a steady shot with the jewelry visible.** A flare from a camera swing, or one that washes out the hand, doesn't count. |
| 17 | **Chainlink face-jump cuts** | **When cutting between takes of the same talking head, pin the eyes to the same screen position** (eye line at y≈440 px of 1920, scale chosen so the pin never hits a frame edge). *His exact wording isn't on file; the rule is as implemented in Chainlink c0141/c0142 `path.py`.* |
| 18 | **Overhead bowl section: use put-ins, not take-outs** (Zaidi 8 v3) | **Check the direction of every action against the story.** In Zaidi 8 the story is her *taking jewelry off and putting it into the bowl*, so every overhead shot must be a **put-in**. Verify by comparing the bowl before and after each hand visit (an item appears = put-in; an item disappears = take-out). v2 had used four take-outs (163.4/166.4/169.3/171.9s: hoop bracelet, silver pavé earring, diamond ring, thin gold ring), which were all replaced with put-ins (177.7, 180.9, 183.9, 186.5, 189.0s). Camera tilts and move-offs (160.8s, 192.2s) are not usable either. |
| 19 | **Show the full lie-down** (Zaidi 8 v3) | **Show the whole action, start to finish.** The lie-down had to go from sitting → legs swing up → stretched out on the couch (59.62–61.32s), not a partial move. Same angle, re-cut to include the complete action. |
| 20 | **Condense the removal section to overhead bowl drops ≥0.5s, no repeats; follow the reference order** (Zaidi 8 v2) | **Condensed montages still obey the rules:** every piece ≥0.5s, no reused moments, order follows the reference. |
| 21 | **Opener: her walking in and sitting** (Zaidi 8 v2) | Reinforces rule 4: open on the person doing something. |

### Documentary-style, off-angle delivery
Some shoots are documentary style: she speaks to an interviewer off camera rather than into the lens. Daniel's guidance (recorded in the style guide, rules 1 and checklist item 1) is that **a steady, consistent off-camera eyeline is a valid real take**. What disqualifies a take is a *drifting* eyeline, glancing down (reading a phone), or a hesitant, quiet read. Don't reject a confident take just because she isn't looking into the lens.

## 2. How the checks were done (so Build can reproduce them)
- **Shake:** high-frequency jitter of the *background* (phase correlation or LK optical flow on the frame with the subject masked out, minus a smoothed path). Zaidi 4 flagged anything above ~6 px jitter at 1080 wide; good single-shot windows were ~0.2 px/frame. View a 4 fps strip around each in/out point.
- **Takes:** loudness, words per second, pauses and a gaze measure (MediaPipe face blendshapes: % of frames looking at lens vs looking down), plus a visual strip of each take (Zaidi 4 `audit.py`).
- **Action direction:** crop the bowl and compare frames before and after each hand visit.
- **Text/logo clearance:** pixel-overlap tests between the overlay mask and her/hands/jewelry across every frame the overlay is on screen.

## 3. Shot-selection checklist (run on every candidate)
1. **Person:** is she in it doing something, delivering confidently, to the lens or at a consistent documentary off-camera eyeline (not reading or glancing down at a phone)?
2. **Steady:** background jitter low (measured on the background; tight crops are stricter)? No operator reposition or angle-finding?
3. **Purpose:** does it show jewelry, her, or the story beat? Would it survive "fewer, stronger shots"?
4. **Direction:** does the action go the way the story needs (putting into, not taking out of, the bowl)? Is the whole action shown (full lie-down)?
5. **Clean:** no real-newspaper text, crew, gear, disliked subjects (chandeliers), or anything that breaks the illusion?
6. **Unique:** not used earlier in the edit, and not a near-duplicate of the previous shot?
7. **Framing:** product or hands fill the frame like the reference? If not, plan a 4K tracked crop (no upscaling).
8. **Timing:** in-point shows the build-up, out-point lands just after the payoff (look-back, flip)?
9. **Position:** opener = her in motion; ending = human closing moment; best still = mid-edit.
10. **Light:** for sun footage, a flare on a steady frame with the rings readable is a bonus; a full washout is not.
11. **Flag:** list any shot you kept despite a concern, so Daniel can decide.

## 3. Oct 18 2026 lessons: narrative Reels (RH-4, Daniel + Build recut)
*Promoted Oct 18 2026 at Daniel's request ("learn from everything we worked on today"). Measurements stay in `LEARNINGS.md`; where a rule cites a section (e.g. L§13) that log is the evidence and wins on numbers.*

Context: v1 (Grok Bot) matched the LOHR reference shot-for-shot as a montage (back-view walk → isolated jewelry details → city close-ups → bench wide → logo). Daniel recut it in Resolve with Build into one evening with the cup as the thread (L§11, L§13). Every rule below is a difference between v1 and his version.

| # | What Daniel did (RH-4) | Rule | Why | Example → fix |
|---|---|---|---|---|
| 18 | Replaced the back-view walk-in opener with her already seated, reaching for the cup (C0133 246–314, 2.30 s, slow push 0.64→0.69) | **Open mid-action, on her face/body doing something.** Not an entrance, not a back view, not a place. | The first frame has to be her and a verb; a walk-in spends a second on getting there. Extends rule 4. | v1 shot 1 C0133 180–236 (back view, walking in) → C0133 246–314 seated reach. |
| 19 | The coffee cup appears in almost every shot, café then city | **Pick one prop that carries the story across locations** (cup, book, newspaper per the shotlist) and keep it in hand through location changes. | Gives a montage continuity and a reason for each cut; the city half reads as the same evening. | v1 city shots without the cup (tight face, fountain back-wide) → square drink, lantern walk with cup, arm+cup at fountain, bench with cup. |
| 20 | Dropped every isolated product insert (bracelet on a closed magazine, JUNGHANS lying on the table, tight chest-and-cup, chin-and-rings) | **Jewelry only on a hand or body that is acting** (lifting a lid, stirring, turning a page, holding the cup) or on her in a place. No still-life inserts. | Jewelry on an acting hand is product + story in one shot; a static insert is a pause. | v1 shot 4 bracelet/magazine C0133 3761 → cut. Shots 7–8 (lift next magazine, page opens onto "Gems on the Rise" bracelet spread) replace it. |
| 21 | Turned the magazine prop into an action that lands on jewelry | **Make a prop's action reveal the product.** | The turn has a payoff; the viewer is led to the jewelry. | C0133 3151–3185 lift → 3341–3372 page lands on bracelet photo. |
| 22 | Split one sip into two shots: wide end-of-sip (1856–1888, full frame) first, tight cup-at-mouth jewelry (1786–1848, zoom 1.29, tilt −247 px) later; no shared frame | **One action may be split into wide-then-tight (or tight later) parts if no frame repeats and the framing changes clearly.** Rule 10 (no repeats) still holds for frames. | Gives the jewelry its own beat without a duplicate. L§13 notes "repeats are fine" did NOT survive. | v1 shot 6 sip 1798–1831 → two non-overlapping windows. |
| 23 | Opening and closing shots 2.30 s; inserts 1.1–1.6 s; overhead 0.83 s; street plate 1.10 s; logo 1.90 s | **Hold first and last shots ~2.3 s; inserts 1.1–1.6 s; a pure "place" plate ~1.1 s.** | The open and close are where the viewer settles; inserts only need the action to read. | v1 shot 1 57 frames → 69; bench 40 → 69. |
| 24 | Cut when the action had read (lid off, page on the bracelet, she has sat down); cuts 4–168 ms off the beat grid | **For narrative/action Reels, cut on completed action, not on the beat.** Keep the music in-point fixed and recut picture against it. | The action finishing is the punctuation. See section 4 note on the beat rule. | v1 cuts were mapped to reference cut times → his cuts land where each action completes. |
| 25 | Swapped the lantern takes (square drink before the lantern walk); street plate (C0129) moved to the middle | **Place-only shots go mid-edit, after she is established, never as the opener.** Order takes so the story moves forward. | An empty plate first is an establishing shot (rule 4); mid-edit it's a breath. | v1 street at shot 10 kept, but after the square/lantern pair. |
| 26 | Ending: she sits down (action, 1.37 s) then a hard punch-in on her on the bench crossing her legs (zoom 1.10→1.02), then the zaidi card | **End on a human action, then the brand card.** The card does not replace the human ending; it follows it. | Consistent with rule 5; Daniel kept the card after recutting everything else. | v1 bench wide (Linden awning, tiny figure) → sit-down + bench punch-in. |

### Framing on narrative Reels (L§11 composition table, L§13 keyframes)
- Base = full upright frame (Resolve zoom 0.5625 on these sideways 4K rolls). Punch in only when the hand/her should dominate: 0.56–0.65 place and whole figure; 0.79–0.90 subject with place; 1.02–1.29 subject fills frame (bench, stir, tight sip).
- **Slow linear zoom ramps across the whole shot**: push-in on the opener (+8%), gentle widen on lid/overhead/reading/street/bench (−5 to −15%). No ease curves are stored (L§13 "Do not call the ramp a bezier").
- He did NOT punch in on the lid, the first sip, the walk or the street.
- v2 reproduction check: an alignment search against his export confirmed every zoom value at both ramp ends (`/workspace/zaidi_rh4/v2/align.py`).

## 4. Note on the beat rule
Montage Reels (Zaidi 4/5): hard cuts on beats, ≥2 beats/shot still apply. Narrative/action Reels (RH-4 type, story with a prop and locations): cut on completed action against a fixed music in-point. If unsure which type a job is, ask Daniel; default to the reference's character. (LEARNINGS §11 lists "ignore beats" as not a general rule; this section scopes it to narrative Reels only.)

## 5. Talking-head (Chainlink) shot/take selection, Oct 18 2026
| # | Daniel's note | Rule | Why | Example → fix |
|---|---|---|---|---|
| 27 | C0141 A v1: "false starts" | **Remove every false start; keep the last complete take of the line.** Find them with a raw per-segment Whisper pass (script-aligned words hide restarts) and n-gram repeats close together. | A restart sounds like a mistake even if the second take is clean. | seg 3 "You thought you had a good idea, and…" restart → start at 101.65; seg 14 "You can, you can run more of what's, you can run more of what's working" → 318.15 (3rd take). |
| 28 | C0141 v2/v3: a repeat dismissed as a transcript glitch was real | **Never dismiss a transcript repeat as a glitch.** Verify with 10 ms energy + short-window transcription on BOTH sides of every join AND inside each segment. | Whole-file transcripts merge or drop repeats; only local checks are trustworthy. | C0143: "and, And Chainlink" inside a segment (641.54 'and' dropped → seg starts 642.2); "calendar only… only has" (2nd 'only' 416.48–416.84 removed). |
| 29 | C0143 v1→v2: "buried under the random…" cut off | **Finish every thought.** A line must end on its last word, not mid-phrase; extend or find a full take. | A half sentence breaks trust in the speaker. | seg end 498.45 → 503.15 ("stuff" 502.36–502.95). |
| 30 | C0143 v2: head-down take | **Speaker must face the lens; reject takes where he looks down for the line.** | Same as rule 1 (real delivery). | 247.65–251.7 head-down → 255.8–257.63 + 258.6–262.4 (needed a new pillar chunk; still pending in v4). |
| 31 | C0143: duplicated "and it's free" | **Cut duplicated lines; keep the strongest/final instance** (the CTA "it's free"). | Repeating a benefit weakens the CTA. | Photographer line hard end 944.85 (after "for you"); CTA "it's free" kept. |
