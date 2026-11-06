# Generation wing (Blender)

**Purpose.** This folder teaches **Editron** filmmaking by generating video in **Blender**. The lessons are what makes a good image, how to retain a character model so it does not change from frame to frame, and what it is like to actually use a camera in a scene, together with environment, characters, and actions.

The working method is to generate and build the scene in Blender, then work the camera in that scene and work the actual actions of the scene in Blender. Editron learns good and bad video, environments, characters, and actions from operating them. Blender is the instrument those decisions are made in. Treating it as a black-box renderer skips the lesson.

The intended run is headless `blender --background --python`. Build scenes from scripted meshes, modifiers, cameras, lights, and actions. Sculpting organic forms is outside this method unless a brief asks for it. Read `blender-notes.md` before writing any script.

This wing is separate from the DaVinci Resolve editing notes at the repository root. A generation job reads this folder. It does not apply editing-wing rules, and it does not copy editing-client notes here.

## Reading order
1. `scene-design.md`: **the most important file.** Camera, scale, continuity, character, environment, and the pitfall of filling in what the brief left unstated. Plus the checklist.
2. `workflow-and-qc.md`: how a job runs, the planned training loop, the feedback history, the pre-delivery QC, and how to deliver.
3. `projects/`: project-specific generation notes, once a project has them. None yet.
4. `look-and-lighting.md`: lighting, materials, atmosphere, time of day, weather. Framework only.
5. `audio.md`: generated and designed sound. Honor the brief, including a no-music brief.
6. `blender-notes.md`: how to carry the rules out in Blender. Read it before writing any Blender script.
7. `assets/`: reusable generation assets (HDRIs, materials, rigs), when they exist.
8. `LEARNINGS.md`: the live session log. It is not a style rule. Promote a line into the file that owns the topic only after Daniel confirms it.

## Standing preferences (apply to every generation job)

Procedural rules, plus the teaching method Daniel set for this wing. Specific lenses, looks, and character designs are not in this list. Those wait for confirmed notes.

### Learn filmmaking by operating the scene
- **A good image is learned by making one** with a camera placed in a built scene. What "good" looks like, as a house rule, is awaiting confirmed notes in `scene-design.md` and `look-and-lighting.md`.
- **Retain the character model.** Once a character is in the scene, that same model stays across frames. It is not rebuilt, replaced, or redesigned from frame to frame.
- **Work the camera, the environment, the characters, and the actions in Blender.** Build the scene, then operate those parts. The render is the record of that work.
- **Blender stays open as a scene, including in a headless script.** The script places the camera and performs the actions on scene data. A render call with no operated scene is the black-box path this wing does not teach.

### On every job
- **Never rewrite a locked prompt** between comparable runs. Comparable runs are generations judged against the same wording. The lock stays byte-for-byte the same. A deliberate wording change is a new lock and a new version, and the report says the wording changed.
- **Flag, don't hide.** Report every miss, every rule that could not be met, and every assumption.
- **Mark UNKNOWN or ASSUMPTION.** An unverified Blender API detail, an unknown path, and an inferred taste are not facts.
- **Version outputs. Never overwrite.** Each run is a new version (`name_vN`). Keep the script that made it.
- **No copyrighted official sound-library rips.** Do not take effects from official or commercial sound libraries that the brief has not licensed for the job.
- **Deliver a report** of what matched the locked wording and what missed.

### Planned training loop
Daniel's sequence for this wing. It is the plan. **Do not execute it now.**

1. Start later with generating character models.
2. Recreate the locked standing generation benchmark inside Blender and iterate until it works well.
3. Only then return to the from-scratch generation benchmark, to see if Editron improved.

The locked standing generation benchmark stays locked. Its contents stay out of this repo. Do not paste the benchmark, and do not paraphrase it, into a script, a log, or a craft file. The same holds for the from-scratch generation benchmark.

## How to use it
- Before a generation: read `scene-design.md`, lock the prompt, and write a scene plan that passes the checklist. The plan names the camera moves and the actions that will be operated in Blender.
- Before a Blender script: read `blender-notes.md`.
- Before delivery: run the QC list in `workflow-and-qc.md`.
- When Daniel gives feedback: add it to the feedback history in `workflow-and-qc.md` as **note → general rule**. Put session measurements in `LEARNINGS.md` in the same pass. Promote a line into the craft file that owns the topic only after he confirms it.
- Leave the planned training loop alone until Daniel opens it. Reading this file is not that opening.

## Provenance
Structured starter, 6 Nov 2026. Daniel set the teaching method above: learn filmmaking by operating scenes in Blender. The craft files are the empty shape for later confirmed lessons. No generation session has been run. No project notes, no measured look, and no benchmark text live in this wing. Where something is unknown or only inferred, it is marked **UNKNOWN** or **ASSUMPTION**.
