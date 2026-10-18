# Workflow, frame-exact cutting, feedback history and QC

## 1. How a job runs
1. **Brief** arrives from Daniel (via the parent agent): Reel number, Notion shotlist row(s), reference Reel URL, music file, footage location on the T7 Shield.
2. **Gather:** script (Google Keep or Notion), Notion shotlist, reference (download, count frames, list cuts, text, music behaviour), music, footage. Low-res proxies first for analysis; 4K trims only for the chosen ranges.
3. **Analyse:** take audit (A-roll), background shake per candidate window, action direction, contact sheets.
4. **Plan:** shot plan / match table (ref shot → clip + source time + frames), cuts on beats.
5. **Build** from 4K sources, crop/track, grade (his LUT by footage type), captions, mix.
6. **QC** (section 5), contact sheet + reference-vs-ours still.
7. **Deliver** with a report: what changed, timings, anything that didn't fit, flagged shots, assumptions. Update the project `PROGRESS.md`.
8. **Daniel grades** the approved cut himself; later passes (text, logo, trims) go **on top of his graded file** without changing his grade or edit.

Files are versioned `name_vN.mp4`, never overwritten; the build script for each version is kept (`render3.py`, `plan3.json`…). Earlier attachments sometimes didn't exist at the filename given in the brief; the file that arrived with the brief (by timestamp) was used and **flagged** each time.

## 2. Frame-exact cutting and the 29.97 vs 30 lesson
- **Zaidi footage is 29.97 fps (30000/1001).** Plan in **frames**, not seconds: Zaidi 6 = 308 frames = 10.277s; Zaidi 7 v2 = 330 frames = 11.011s; Zaidi 8 = 603 frames = 20.12s; Zaidi 5 v6 = 525 frames = 17.518s; Zaidi 3 = 1744 frames = 58.19s. Convert with `t = n × 1001/30000`.
- **Chainlink is 30 fps exactly.** Its pipeline snaps every piece to the 1/30 s grid and conforms sources with `fps=30:round=near`.
- **The drift problem:** treating 29.97 material as 30 fps (or mixing the two in one timeline) makes cut points slide by 1 frame every ~33 s (0.1%), so cuts land off-beat and audio and video lengths diverge. Keep the timeline at the source rate, compute cut frames from beat times at that rate, and check audio duration = video duration on every export. **NOTE:** the brief for this repo names this as a lesson Daniel taught, but the specific incident isn't written in any saved note. The rule above is the practice that the notes do show.
- Snap cuts to beat frames and verify after render that every cut is at its planned frame (Zaidi 8 QC: "16 cuts exactly at the planned frames").
- Reference Reels are often 24 or 25 fps; convert their cut times to seconds, then to our frame grid.

## 3. Feedback history (each correction → the rule it became)
| Project / version | Daniel's note (quote or close paraphrase) | Rule |
|---|---|---|
| Zaidi 4 v2 | One A-roll line was a rehearsal / phone-reading take | Real delivery takes only; audit gaze + delivery |
| Zaidi 4 v3 | "Wobbly shot 42s from end" | Background-shake metric on every shot; replace, don't stabilise |
| Zaidi 4 v3 | "Long gap at 26s from end" | No dead air; no silent title cards |
| Zaidi 4 v4 | Replace the chandelier shot; the regrade was "abysmal" | Remove every shot of a disliked subject; regrade conservatively |
| Zaidi 4 v5 | Use her audio for the L4 tail instead of a title card; orbit shot on "So come celebrate with us" | Layer VO under B-roll; moving A-roll on the emotional line |
| Zaidi 5 v1 | Choppy; start the music at the lyrics | Whole beats; music at vocal entry |
| Zaidi 5 v2 | Tighten pace; opener should have her in it; end on her walking away through the car window; knees-down crop of the walking-past-car shot; keep the hand-in-hair portrait but not last | ~1.1s average; open on her in motion; human ending; tracked 4K detail crops; best still mid-edit |
| Zaidi 5 v3 | Delete the ~15s and ~16s shots, cut shot 2, one over-the-paper shot then the wide; swap the shaky feet shot; crop LAUNCH EVENT to the fake sheet; paper SFX too much | One idea per shot; detail shots judged for shake; crop to the intended prop; one subtle SFX |
| Zaidi 5 v4 | Last shot: include the turn and look-back, land just after it, ~3s; cut real-newspaper shots, use the rapid flip cropped, flag others | Trim around the payoff; check every frame for illusion-breaking props |
| Zaidi 5 v6 | Cut seconds ~17–18 of the walk-away (on his graded file) | Only 1.2s (36 frames) was possible without a flash frame or cutting into the turn. Reported, not forced |
| Zaidi 6 v1 | Crop in so her hands fill most of the frame | Match the reference framing; 1.8x tracked crop from 4K |
| Zaidi 6 v3 | Text overlay brief: elegant, safe zones, clear of hands, ≥1.5s, no box | Captions rules in clients/zaidi.md |
| Zaidi 7 v1 | Skip the first part; "I was still finding the angle" | Never include angle-finding; shorter is fine |
| Zaidi 8 v1 | Opener walk-in and sit; condense the removal to overhead drops ≥0.5s with no repeats; follow the reference order; grade too warm and gold, bowl blown out (low LUT mix or neutral) | Reference order; montage pieces ≥0.5s, unique; protect highlights |
| Zaidi 8 v2 | Take-outs used instead of put-ins; show the full lie-down | Check action direction; show the whole action |
| Zaidi 8 v3 | (Daniel graded it himself) | Learn his interior look (#8 LUT) |
| Zaidi 8 v4 | Drop "A SERIE"; fix line spacing, tracking, "Boring" placement, centring (check with pixel measurements); use the established caption style exactly; centre the logo and make it 1.3–1.5x larger, crisp | Pixel-measured typography; reuse approved caption roles; logo rules |
| Phone Reel ADR v8 | Swap only her lines, keep caller/music/SFX, match sync, captions follow words | ADR method in audio.md |
| Phone Reel v9 | "Westwood" dropped out; make her sound like a phone | Gain over the whole piece; phone EQ chain |
| Phone Reel v10 | Echo on her voice | Dereverb raw take (WPE → DeepFilterNet), no added reverb |
| Chainlink promos | Face jumped between takes; slow stat scenes; soda caption; analytics shown as real | Eye pinning; cut slow scenes; realistic content; SAMPLE DATA / COMING SOON tags |

## 4. Mistakes Daniel had to correct, and how to avoid them
| Mistake | Avoid it by |
|---|---|
| Used a rehearsal or phone-reading take | Gaze + delivery audit of every take before building |
| Shipped wobbly shots / angle-finding | Background-shake metric on every shot; 4 fps strip of in/out points |
| Kept disliked subjects / illusion-breaking props | Scan the contact sheet for props and recurring subjects; flag |
| Opener without her; best shot last; ending trailed past the look-back | Opener and ending rules (shot-selection 4–7) |
| Choppy, then too slow | 1.0–1.2s average on whole beats; no shot under 2 beats |
| Music not at the lyrics; silent tail | Find vocal/drop entry; audio duration = video duration |
| Too much SFX | One subtle effect at most, tied to an action |
| Grade too heavy (Z4 v4), milky (Z7), too warm with blown bowl (Z8 v1), lifted/cooled (Z8 v3) | Pick his LUT by footage type; compare stills against his graded references |
| Framing looser than the reference | Reference-vs-ours still before delivery; crop from 4K |
| Wrong action direction (take-outs) | Before/after bowl comparison per hand visit |
| Inventing typography | Reuse the approved caption roles; measure spacing in pixels |

## 5. Pre-delivery QC checklist
1. Resolution 1080×1920 (or 2160×3840 for masters on his 4K grade), 29.97 (Zaidi) or 30 (Chainlink) fps; frame count as planned; length matches the brief/reference.
2. No flash frames; every cut on a beat at its planned frame; no repeated shots or source ranges.
3. Shake checked on the background of every shot, including crops; no repositioning.
4. Opener has her; ending is the human closing moment; no prop or real-paper leaks; action directions correct; full actions shown.
5. Audio: music from the right point to the last frame, short tail fade, −14 LUFS (Zaidi), A/V durations equal, SFX subtle, no clipping (peak ≤ 1.0).
6. Grade: right LUT for the footage, mids 0.25–0.45, highlights not clipped, warm skin, no blue. On his graded file: untouched sections match his frames.
7. Crops from 4K (no upscaling); 1:1 sharpness check; tracked crops smooth, translation only.
8. Text: exact words, ≥1.5s for sentences, safe zones (10–80% height, right buttons), clear of face/hands/jewelry by pixel test, captions match the audio.
9. Chainlink: real screens only, tags, safe zones, no overlaps, eye pin without edge exposure.
10. Contact sheet + reference-vs-ours still made and looked at; PROGRESS.md updated; risky shots and assumptions listed in the report.

## 6. Delivering
- **Masters:** 4K on top of his grade: HEVC 10-bit (`libx265 -crf 10–12 -pix_fmt yuv420p10le -tag:v hvc1`), bt709 tags, audio stream-copied from his file.
- **Share copies:** under 200 MB, H.264 8-bit (e.g. 4K at 45 Mbps, high profile 5.1) for compatibility.
- **1080 edits:** H.264 yuv420p (crf ~14–16), AAC 256–320k 48 kHz, `+faststart`.
- Hand the file paths plus a report: changes, timings, offsets, what didn't fit, flagged shots. Never send or post anything externally without Daniel's approval.

## 7. Practical environment notes
- Daniel's Mac (where the T7 Shield mounts) runs zsh with ffmpeg at `~/bin/ffmpeg`. Copy jobs to the editing box are capped at 100 MiB per file and only from `~/` paths.
- Image previews cache by filename; always write new still filenames when iterating.
