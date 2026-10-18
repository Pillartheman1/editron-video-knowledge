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
