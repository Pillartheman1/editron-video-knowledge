# Look and lighting

Framework for the generated picture: lighting, materials, atmosphere, time of day, and weather. Parallel to the editing wing's colour file, and it does not inherit that wing's grades, LUTs, or node trees.

No measured generation look exists. A house grade, a house palette, and a default time of day are **awaiting confirmed notes**. Do not invent them. Learn a good image by operating the lights in the built scene and comparing the frames to the locked wording (`scene-design.md`, `README.md`).

If the lock is silent on a lighting choice, record what was used, mark it **ASSUMPTION**, and keep it across comparable runs. An extra light added so the frame "reads" is an unstated extra.

## 1. Lighting
- Build only the sources the lock names (sun, practicals, a stated direction, a stated quality).
- Place those lights in the Blender scene and leave their values in the saved file so the lighting can be inspected, the same way the camera is inspected.
- Exposure, if the brief states one, is part of the lock. A default exposure is **UNKNOWN**.
- A confirmed lighting setup, when Daniel states one, lands in the table in section 6. Until then the table stays empty.

## 2. Materials
- A named surface (stone, cloth, metal, skin) gets a material that serves that name. The shader values are **ASSUMPTION** unless the brief gives them, and they are recorded.
- One material assignment per surface, held across frames. Do not re-roll a material per frame. That is the same failure as changing a character model mid-shot.
- Texture files come from `assets/` or from a path the brief gives, with the license noted. A downloaded texture whose license is unknown stays out.

## 3. Atmosphere
- Fog, haze, dust, and volumetrics are in the scene only when the lock asks for them.
- Density and colour, when unstated, are **ASSUMPTION**, recorded, and held still across comparable runs.
- Atmosphere that exists to hide a modeling problem is an extra. Flag the modeling problem instead.

## 4. Time of day
- Use the time of day the lock states, and keep it continuous across cuts unless the lock changes it.
- Sun direction has to agree with that time of day. A noon prompt with a sunset key is a miss.
- A default time of day for this wing is **awaiting confirmed notes**.

## 5. Weather
- Weather is in the scene only as specified: clear, rain, storm, snow, wind, or whatever word the lock uses.
- If the weather makes sound (rain, storm), the audio brief has to agree (`audio.md`). Picture weather with no sound, or sound with no picture, is a miss when the brief called for the event.
- Do not add weather to explain a mood the lock did not state.

## 6. Confirmed looks

**Awaiting confirmed notes.** No generation look has been measured or confirmed.

| # | His note (project) | General rule |
|---|---|---|
| | | |

## 7. Checklist
1. Every light in the scene is named by the lock, or listed as an **ASSUMPTION** that matches the comparable run.
2. Materials match the named surfaces and do not change per frame.
3. Atmosphere matches the lock, including its absence.
4. Time of day is consistent across cuts and agrees with the sun.
5. Weather matches the lock and agrees with `audio.md`.
6. Nothing here was copied from an editing-wing grade.
