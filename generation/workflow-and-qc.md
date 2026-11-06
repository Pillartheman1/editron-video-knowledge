# Workflow, generation QC, feedback history

How a generation job runs in this wing. The editing wing's cutting and delivery rules do not apply here.

The method on every job: build the scene in Blender, then work the camera and the actual actions in that scene. The render is the record. Blender is not a black-box renderer. See `README.md` and `blender-notes.md`.

## 1. How a job runs
1. **Brief** arrives from Daniel: what the piece is, the prompt or board, any model or asset he supplies, the audio brief, and the tool if he names one besides Blender.
2. **Lock the prompt and the board.** Freeze the wording, and freeze any board he names as the spec, before the first generate. Comparable runs use those bytes. Do not rewrite a locked prompt between them. A deliberate wording change is a new lock and a new version, and the report says so. Copy the lock into the version folder so later runs can prove it was unchanged.
3. **Generate in Blender, by operating the scene.** Build the environment and place any supplied character model. Then work the camera in that scene and work the actions on those models (`scene-design.md`, `blender-notes.md`), headless via `blender --background --python`. If the brief names another generation tool for a stated part, use it only for that part, say so in the report, and do not let it replace operating the Blender scene. Do not swap tools between comparable runs.
4. **Compare against the locked wording.** Walk the lock line by line. For each line, say matched or missed, and point at the frame or time. "Looks right" is not the compare.
5. **QC** (section 5), on the frames and on the saved scene.
6. **Deliver** a new version plus the report of what matched and what missed (section 6).
7. **Log feedback** as **note → general rule** in section 3 when Daniel comments on a delivered version. Session measurements go in `LEARNINGS.md` in the same pass. Promote a line into `scene-design.md`, `look-and-lighting.md`, `audio.md`, or `blender-notes.md` only after he confirms it.

Character-model generation is not part of this loop yet. If the lock needs a character and none was supplied, flag **UNKNOWN** and stop. Do not design one to keep the job moving (`scene-design.md` §4).

## 2. Planned training loop

Daniel's curriculum for this wing. **Do not execute it now.** Running a normal job in section 1 is not permission to start this sequence.

1. Start later with generating character models.
2. Recreate the locked standing generation benchmark inside Blender and iterate until it works well. Each iteration still operates the camera and the actions in the scene, and still keeps the benchmark's wording locked.
3. Only then return to the from-scratch generation benchmark, to see if Editron improved.

Do not paste either benchmark into this repo, a script, a report, or `LEARNINGS.md`. Refer to them by those names. Where the locked text is stored is **UNKNOWN** until Daniel points at it. Do not reconstruct it from memory.

## 3. Feedback history (each correction → the rule it became)

No rows yet. Add one only when Daniel comments on a delivered generation. Quote or closely paraphrase his note. Do not backfill rows from guesses.

| Project / version | Daniel's note (quote or close paraphrase) | Rule |
|---|---|---|

## 4. Mistakes to avoid

Structural guidance for this wing. These rows are not a history of corrections. No generation mistake has been logged yet.

| Mistake | Avoid it by |
|---|---|
| Rendered without operating the scene | Build the scene, then place the camera and keyframe the actions in Blender before the render |
| Character changed between frames | Keep one model datablock for the whole shot; do not regenerate or swap it per frame |
| Rewrote the locked prompt between comparable runs | Copy the lock into the version folder and leave those bytes alone |
| Started the training loop, or pasted a benchmark | Section 2 stays closed until Daniel opens it; name the benchmark, do not copy its text |
| Reported a match the frames do not show | Line-by-line compare; flag, don't hide |
| Overwrote a previous output | Write `name_vN` for the picture, the scene file, and the script |
| Filled in a person, prop, weather, logo, or action the lock did not state | Leave it out (`scene-design.md` §7) |
| Invented a lens, a look, or a character design and filed it as a rule | Keep it as **ASSUMPTION** on the job, or wait for a confirmed note |
| Used a ripped official or commercial sound library | `audio.md`: only audio the brief licenses |
| Stated an unverified Blender call as fact | Mark **ASSUMPTION** and confirm it against the installed version |

## 5. Pre-delivery QC checklist
1. **Prompt unchanged.** The lock in the version folder matches the bytes that were frozen. The report says so.
2. **Continuity.** Model, wardrobe, named props, time of day, weather, and screen direction hold across cuts.
3. **Character match.** The character matches the locked description, and the same model is on every frame it appears. It was not rebuilt mid-shot. No character was invented.
4. **Camera match.** Viewpoint, lens, and move match the lock. The camera was operated in the Blender scene (the saved scene shows the same camera the frames show).
5. **Audio brief honored.** Music, rain, storm, footsteps, and other effects are present only as specified. A no-music brief means no music (`audio.md`).
6. **No invented extras.** No person, prop, caption, logo, weather, move, or action beyond the lock.
7. **Scene was operated.** The version includes the script and the saved scene, so the camera and the actions can be inspected. A movie with no scene file fails this check.
8. **Versioned.** Paths are new. Nothing from a previous version was overwritten.
9. **Report.** What matched, what missed, every flag, every **ASSUMPTION**. Blender's printed version is in the report.

## 6. Delivering and versioning
- Each delivery is a new version: picture, saved `.blend`, and script, named `name_vN`. Never overwrite.
- The report covers, in the lock's own terms: camera, actions, character retention, environment, audio, extras (this list is empty when the scene is clean), assumptions, and the Blender version the script printed.
- Codec, container, resolution, and frame rate follow the brief. If the brief is silent, record the choice as **ASSUMPTION** and hold it across comparable runs. House delivery numbers are **awaiting confirmed notes**.
- Do not publish a render to a destination the brief did not name.
- Do not include benchmark text in the report. If a future job is the benchmark recreation, name "the locked standing generation benchmark" and describe only the match and the miss.

## 7. Practical environment notes
- Blender's install path, version, GPU, and enabled add-ons are **UNKNOWN** on the machine that will run the job. The script prints `bpy.app.version_string` at start. **ASSUMPTION:** that attribute exists in background mode.
- Do not assume a path from the editing wing exists here.
- Image and frame reviews use new filenames per iteration, so a cached preview cannot pass for the new version.
