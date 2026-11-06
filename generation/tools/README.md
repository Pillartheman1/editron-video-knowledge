# Tools

Helper scripts for the generation wing live here once one has been run and kept.

This folder starts empty. Read `blender-notes.md` before adding a Blender script. A script here must:

- run headless (`blender --background --python`)
- build a scene, then operate the camera and the actions in that scene
- retain a character model across frames when a character is in the scene
- refuse to overwrite an existing output
- print the Blender version and every **ASSUMPTION**
- leave the locked prompt untouched, and keep benchmark text out of the script

Job-specific scripts stay with the job until they are reusable. A script that encodes a look, a lens, or a character design Daniel has not confirmed does not belong here. Generating character models waits for the planned training loop in `../README.md`.
