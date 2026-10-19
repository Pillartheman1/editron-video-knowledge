# Translating these rules into DaVinci Resolve

Everything so far was built with ffmpeg/Python/Remotion, not Resolve. This file maps the rules onto Resolve. Items marked **ASSUMPTION** are not verified against Daniel's setup. Confirm them against his real projects first.

## Project and timeline settings
- **Vertical timeline:** 1080×1920 for edits (Zaidi masters on his grade are 2160×3840). Check "use vertical resolution" in the timeline format.
- **Frame rate:** Zaidi **29.97** (match the Sony sources), Chainlink **30**. Set the timeline rate *before* importing; Resolve can't change it later. Never mix the two (see the drift lesson in `workflow-and-qc.md`).
- **Sideways clips:** rotate 90° in Inspector (Zaidi 2 clips needed ffmpeg `transpose=2`, i.e. 90° counter-clockwise). **ASSUMPTION:** check each clip's orientation metadata.
- **Colour management:** the LUTs were fitted on Rec.709 gamma 2.4 footage as full-range RGB. Use a Rec.709 timeline (DaVinci YRGB, not colour-managed), or put the LUT where the signal is Rec.709. **ASSUMPTION:** Daniel's colour-science settings are unknown; read them from his project.
- Data levels: leave clips on Auto/Video levels so Resolve converts to full range internally, as the LUT fits assume.

## Scripting API basics (Resolve Python API)
- `resolve = dvr_script.scriptapp("Resolve")`, `pm = resolve.GetProjectManager()`, `project = pm.GetCurrentProject()`, `mp = project.GetMediaPool()`.
- Import: `mp.ImportMedia([paths])`. Build: `mp.CreateEmptyTimeline(name)` then `mp.AppendToTimeline([{"mediaPoolItem": item, "startFrame": s, "endFrame": e}])`. Use **frame numbers** computed at the clip's rate.
- Markers on beats: `timeline.AddMarker(frame, "Blue", "beat", "", 1)`.
- LUTs: copy the `.cube` files into Resolve's LUT folder, refresh LUTs, then `timelineItem.SetLUT(nodeIndex, "daniel_zaidi8.cube")`.
- Render: `project.SetRenderSettings({...})`, `project.AddRenderJob()`, `project.StartRendering()`.
- **ASSUMPTION:** API details vary by Resolve version; check the README in Resolve's Developer/Scripting folder for the installed version.

## Colour page conventions
- Suggested node tree per clip (**ASSUMPTION**, until Daniel's trees are available): `01 exposure/balance trim` → `02 Daniel LUT (#5/#7/#8 by footage)` → `03 highlight rolloff / whites` → `04 vignette (only #7/#8 looks)`. Use node Key Output Gain to set LUT strength when a partial mix is needed (the equivalent of the ffmpeg blend).
- One look across the Reel; use a group or shared node for the LUT, plus per-clip exposure trims.
- **Learn from his nodes:** when Daniel's graded `.drp` or PowerGrade stills are available, export his node graphs and copy their structure and values rather than these fitted LUTs.

## Edit page conventions
- Hard cuts on beat markers; no transitions on Zaidi montages unless the reference has them.
- Tracked crops: Inspector Zoom/Position keyframes, or Fusion transform with a tracker (translation only, smoothed ~12–18 frames). Crop only from 4K sources.
- Never repeat a source range: keep a used-range list (clip, in, out) while building.
- Titles: Fusion Text+ with Libre Baskerville Bold / Poppins (install the `.ttf` files on the Resolve machine), cream `#FBF9F4`, gold gradient for B words, soft drop shadow (no box). Match the sizes in `clients/zaidi.md` (halve for 1080).

## Fairlight / audio
- Music on its own track from the vocal/drop point; tail fade 0.28–0.5s; normalise the deliverable to −14 LUFS (Zaidi). Use a loudness meter on the bus.
- Phone-voice EQ and dereverb chains (audio.md) were done offline with ffmpeg/Python; in Resolve, render those stems first and import them. **ASSUMPTION:** Fairlight equivalents weren't tested.

## Deliver page
- Masters: H.265 10-bit 4K or ProRes; share copies H.264 under 200 MB; bt709 tags; AAC 48 kHz.

## Oct 18 2026: what is now confirmed (read LEARNINGS.md §1–§6, §13 first)
These replace the ASSUMPTIONS above where they conflict. LEARNINGS.md is the authority.
- **Two Resolve apps** on the Mac: **Studio** `/Applications/DaVinci Resolve/DaVinci Resolve.app` (bundle `com.blackmagic-design.DaVinciResolve`, 21.0.0b) holds the projects; **Lite** `/Applications/DaVinci Resolve.app` is the wrong one. `open -a "DaVinci Resolve"` launches Lite. (Grok Bot's RH-4 v1 report wrongly called it "the free version".)
- **Scripting does not connect on his Mac**: `scriptapp("Resolve")` returns None even in Studio with External scripting = Local; a Fusion probe printed RESOLVE_NIL; TCP 1144 not listening. Don't retry it. Build's working method is editing the project `Project.db` with Studio quit (L§2–§6). The RH-4 v2 in-app script (`/workspace/zaidi_rh4/rh4_resolve.py`, installed under Fusion/Scripts/Utility) is untested for that reason.
- **Sideways Sony rolls**: Inspector Rotation **+90** (not −90) and Zoom **0.5625** (9/16) = full upright 1080×1920 frame under cover scaling (L§4). Zoom 0.5 (our v1 guess) and 16/9 are wrong. An upright 1080×1920 end card uses rotation 0, zoom 1 (his RH-4 card is keyed 1.28→1.16).
- **Pan/Tilt** are inspector pixels (stored fraction × 1080 / × 1920, L§13). Empirically, on a 2.29× punch-in the crop centre moved ≈0.45 × tilt px (negative = up) and ≈ −1.6 × pan px horizontally; sign not checked in the viewer.
- **API name fix**: there is no `MediaPool.AppendToMediaPool`; clipInfo with startFrame/endFrame goes to `MediaPool.AppendToTimeline` (endFrame inclusive; `mediaType` 1 video, 2 audio; `recordFrame` for placement). Transform keyframes cannot be set via the API (ramps are manual or via Project.db, L§13).
- **Full files in the pool, cuts via In/duration** (Daniel's correction, L§5). Never import pre-rendered trims.
- **His real node tree (RH-4)**: curves → primary wheels → ResolveFX Glow → ResolveFX Vignette (Advanced), pasted per clip, two wheel presets by setup, no LUT (L§13). This replaces the suggested tree above for this kind of reel; the fitted cubes are estimates. To reuse his grade in a new timeline, `TimelineItem.CopyGrades([targets])` from his graded items (untested here) beats a LUT.
- ffmpeg reproduction of his RH-4 grade (for review files only): fitted per-look cubes `/workspace/zaidi_rh4/rh4_look{A,B,S}.cube` + radial vignette mask; `blend` must run in gbrp or the chroma gets multiplied (green frames).
