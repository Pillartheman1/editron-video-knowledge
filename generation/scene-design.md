# Scene design (the most important file)

This is the generation wing's scene file, parallel to the editing wing's shot file, and it does not inherit that wing's rules. Read it before building.

The picture is judged from a scene Editron actually operates: a camera placed in the scene, actions performed on the models in that scene, and a character model that stays the same from frame to frame. That is how this wing learns what makes a good image. This file does not define the look. House rules for lens, height, move, palette, character design, and pacing are **awaiting confirmed notes**.

Nothing below is a measured generation style. Where a choice is not in the locked prompt, mark it **ASSUMPTION** or **UNKNOWN** and hold it still across comparable runs.

Format, once a note exists: **his note (project)** → **general rule**. There are no notes yet.

## 1. Camera, lens, and viewpoint

The camera is a camera in the scene. Place it, choose the lens the lock names, and move it through the action. The render records that operation.

- **Viewpoint.** Use the height, angle, and screen position the locked prompt states. If the lock is silent, write down the viewpoint used, mark it **ASSUMPTION**, and keep it for every comparable run.
- **Lens.** Use the focal length or field of view the lock states. A default lens for this wing is **awaiting confirmed notes**.
- **Move.** A move (hold, pan, track, push) happens only when the lock asks for it, and it is keyframed on the camera in the scene. An unstated move is an extra.
- **Match.** Comparable runs keep the same camera. Changing the lens or the position between them breaks the comparison. A new camera is a new version, named as such.

## 2. Scale and distance

Scale is part of operating the camera and the scene. A stated height, distance, or relative size belongs to the lock. Keep it across cuts and across comparable runs.

If the brief gives no measurement, record the unit scale and the distances used, mark them **ASSUMPTION**, and reuse those numbers on the next comparable run. Do not rescale a character or a set between shots to "improve" a frame. A house scale is **awaiting confirmed notes**.

## 3. Continuity across cuts

A cut continues the same scene. What the lock has already established stays established:

- the same character model (section 4)
- wardrobe and props the brief named
- time of day and weather (`look-and-lighting.md`)
- screen direction and the side of the action the camera is on

A later shot does not redesign those to make a stronger frame. If two shots in the lock contradict each other, flag the contradiction and do not pick a side silently. Continuity taste beyond "hold what was established" is **awaiting confirmed notes**.

## 4. Character consistency

**Retain the character model.** Once a character is in the scene, that same model is the character on every frame. Do not rebuild the mesh per frame, swap in a new generation, or redesign the face, body, or costume between frames of a shot.

Across cuts, the character is the same model again, with only the action changing. A second model that "looks similar" is a different character. Flag it.

Generating character models is step 1 of the planned training loop in `README.md`. That step starts later. Do not open it because a shot feels empty. If the lock needs a character and no model has been supplied, stop and flag **UNKNOWN**. Inventing a hero design is an unstated extra.

How a confirmed character should be built, costumed, or proportioned is **awaiting confirmed notes**.

## 5. Environment and physics

The environment is built in the scene and then left alone except for actions the lock names. Ground, architecture, sky, and props come from the locked wording. Operating the environment means placing those parts at the locked scale and keeping them continuous. It does not mean dressing the set with extra detail.

Physics (a fall, a cloth, a splash, a collision) is performed when the lock states that event, on the objects that event involves. An unstated simulation is an extra. Solver settings, if a brief forces a simulation, are **ASSUMPTION** until measured, and they stay fixed across comparable runs. A house physics look is **awaiting confirmed notes**.

## 6. Actions

Actions are performed in Blender on the scene's objects. The lock names the verb (who does what). The script keyframes that verb on the retained model. Describing the action only in a prompt, or rendering a still pose and hoping the motion reads, skips the lesson.

An action the lock does not name stays out. Timing and easing, as a house taste, are **awaiting confirmed notes**. Until then, use the timing the lock states. If it states none, record the keys, mark them **ASSUMPTION**, and keep them across comparable runs.

## 7. Do not fill in unstated extras

The failure mode of this wing is completing a picture the brief did not ask for. If the lock does not mention a person, a prop, a vehicle, weather, a logo, a caption, a crowd, a camera move, or a second action, it is not in the scene. A frame that feels thin is flagged. It is not filled.

A reference image counts only when the brief adopts it as part of the lock. Detail visible in some other picture is not permission to add it.

## 8. Style rules

**Awaiting confirmed notes.** No house lens, camera height, camera move, palette, character design, or pacing is confirmed. Do not add a row here until Daniel confirms the line and it is promoted from `LEARNINGS.md`.

| # | His note (project) | General rule |
|---|---|---|
| | | |

## 9. Scene-design checklist

Run this on the scene before render, and again on the frames before delivery.

1. **Operated camera.** The camera was placed in the Blender scene. Viewpoint, lens, and move match the lock. Anything the lock left unnamed is an **ASSUMPTION**, written down, and identical to the comparable run.
2. **Scale and distance.** Stated sizes and distances match. Unstated scale is the recorded assumption, held still.
3. **Continuity.** Across cuts, the established model, wardrobe, props, time of day, weather, and screen direction still hold. Contradictions are flagged.
4. **Character.** The same model is present on every frame it appears. It was not regenerated or redesigned mid-shot. No character was invented to fill a gap.
5. **Environment and physics.** The set matches the lock. Physics runs only for events the lock names.
6. **Actions.** Each verb in the lock was keyframed on the retained model. No extra action was added.
7. **Extras.** No person, prop, weather, logo, caption, or event beyond the lock.
8. **Flag.** List anything kept despite a concern, so Daniel can decide.
