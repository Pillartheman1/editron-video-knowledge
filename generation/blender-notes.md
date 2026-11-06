# Translating these rules into Blender

Read this file before writing any Blender script. The craft rules live in `scene-design.md`, `look-and-lighting.md`, `audio.md`, and `workflow-and-qc.md`. This file is only how to carry them out.

**Blender is the scene Editron operates, including from a script.** Generate and build the scene, then work the camera in that scene and work the actual actions on the models. The lesson (a good image, a character that holds still from frame to frame, what a camera does, plus environment, characters, and actions) comes from that operation. A script that jumps to a render, or that sends the prompt to another generator and only saves the movie, treats Blender as a black box. That is the wrong shape for this wing.

The intended method is headless:

```bash
blender --background --python script.py
```

**ASSUMPTION:** the `blender` binary is on `PATH`. The installed version is **UNKNOWN**. Print it before building. Operator and attribute names below are the commonly documented `bpy` surface. Treat every one of them as **ASSUMPTION** until the printed version accepts them. API details move between versions. Passing the lock path and the output path after `--` is also **ASSUMPTION**: confirm that Blender leaves those arguments in `sys.argv`.

The planned training loop in `README.md` is not open. Do not generate character models as a training step, and do not paste the locked standing generation benchmark (or the from-scratch generation benchmark) into a script. When that loop is opened later, the benchmark is recreated by operating a scene under this file, with its wording left locked.

## 1. Script shape
A job script should:
1. Print the Blender version before it builds anything.
2. Read the locked prompt from the path it was given. Do not embed a second copy of the wording, and do not rewrite it.
3. Refuse to run if the output path already exists.
4. Build only the environment, lights, and supplied models the lock names.
5. Operate the camera (section 3) and the actions (section 6) on that data.
6. Save the `.blend` to the version path, then render to a different versioned path.
7. Print a short summary of camera, objects, lights, frame range, and every **ASSUMPTION**, so the report can say what matched.

**ASSUMPTION:** `bpy.app.version_string` is available in background mode. **ASSUMPTION:** arguments after `--` are left in `sys.argv`.

Saving the scene is part of the method. The movie is the record. The `.blend` is the operated scene a later pass inspects. **ASSUMPTION:** `bpy.ops.wm.save_as_mainfile(filepath=...)`.

## 2. Scene setup
- One script and one `.blend` per version. Never overwrite.
- **ASSUMPTION:** `bpy.ops.wm.read_factory_settings(use_empty=True)` clears the default startup file. Background mode may already have a startup scene. Confirm, then remove anything the lock did not name.
- Units and frame rate follow the brief. If it states none, choose once, mark **ASSUMPTION**, and hold those values across comparable runs. **ASSUMPTION:** `scene.unit_settings`, `scene.render.fps`, `scene.render.fps_base`, `scene.frame_start`, `scene.frame_end`.
- Resolution follows the brief. **ASSUMPTION:** `scene.render.resolution_x`, `resolution_y`, `resolution_percentage`.
- Colour management is **UNKNOWN**. Do not import a look from the editing wing. Record the view transform you left in place.

Build from scripted meshes and modifiers. Sculpting organic forms is outside this method unless the brief asks for it. **ASSUMPTION:** mesh data is created with `bpy.data.meshes.new` and edited with `bmesh`. Modifier stacks are preferable to a destructive sculpt for anything that must stay identical across frames.

## 3. Cameras
Operating the camera means the script sets the camera's transform and lens on scene data, and keyframes them when the lock asks for a move.

- **ASSUMPTION:** `bpy.data.cameras.new`, then an object via `bpy.data.objects.new`, linked into the scene, assigned to `scene.camera`.
- **ASSUMPTION:** lens in millimetres is `camera.lens`. Field of view is `camera.angle`. Sensor size is `camera.sensor_width` / `sensor_fit`.
- **ASSUMPTION:** location and rotation live on the camera object and are stored with `keyframe_insert`.
- Name the camera after the shot. One operated camera per locked viewpoint.
- Depth of field stays off unless the lock asks for focus falloff. **ASSUMPTION:** `camera.dof`.
- Print the camera's location, rotation, and lens at each key so the compare can check them against the lock.
- Do not change lens or position between comparable runs.

A house lens and a house camera height are **awaiting confirmed notes**.

## 4. Lighting and world
Place only the lights `look-and-lighting.md` allows. **ASSUMPTION:** `bpy.data.lights.new(name, type)` with type one of `POINT`, `SUN`, `SPOT`, `AREA`; energy and colour on the light data.

World background and HDRI only when the brief or `assets/` supplies one. **ASSUMPTION:** `scene.world` node tree. An HDRI path is **UNKNOWN** until a file is actually in `assets/` with a license note.

## 5. Materials
**ASSUMPTION:** a material is `bpy.data.materials.new` with `use_nodes` and a Principled BSDF. Do not invent a house shader. Record every value the brief did not state, and assign the material once. Do not re-roll it inside the frame loop.

## 6. Characters, assets, and actions
**Retain the character model.** The character is one object and one mesh datablock for the whole frame range. Animate that object. Do not rebuild or replace `object.data` inside the frame loop. A per-frame regenerate is the bug this wing exists to unlearn.

- A model the brief supplies is appended or linked once. **ASSUMPTION:** library load and the glTF / OBJ / FBX importers differ by version and by enabled add-ons. Confirm the operator exists before calling it. If the importer is missing, stop and flag. Do not substitute a new mesh.
- Generating a new character model is step 1 of the planned training loop. It is not open. If the lock needs a character and no model was supplied, exit with **UNKNOWN**. Do not sculpt or script a stand-in hero.
- Props and set pieces that the lock names may be scripted meshes. They stay the same datablocks across frames too.
- Actions are keyframes on those objects (or one action datablock reused across the shot). **ASSUMPTION:** `object.keyframe_insert(data_path, frame=n)` and `bpy.data.actions`. Interpolation defaults are **UNKNOWN** until printed; set them explicitly when the lock cares, and record them.
- Physics runs only for an event the lock names. **ASSUMPTION:** a bake step is required before a background render, and the operator name differs by version. Bake, then render. Do not add a simulation the lock did not ask for.

## 7. Rendering
Render after the scene is operated and saved.

- **ASSUMPTION:** `scene.render.engine`. The enum for Eevee versus Eevee Next versus Cycles depends on version. Use the engine the brief names. If it names none, record the choice as **ASSUMPTION** and keep it across comparable runs.
- Output path includes the version and is not the `.blend` path. **ASSUMPTION:** `scene.render.filepath` and `image_settings.file_format`. An animation render is **ASSUMPTION:** `bpy.ops.render.render(animation=True)`.
- Refuse if that filepath's directory already contains a finished version of this `vN`.
- Still frames used for the compare get new filenames per iteration.

## 8. Before you run a script
1. `scene-design.md` checklist is answered on paper, including the extras check.
2. The lock file is the one copied into the version folder, unchanged.
3. The output paths are new.
4. No character generator is being started, and no benchmark text is in the script.
5. Every API call you are unsure of is marked **ASSUMPTION** in the script's printed summary.
6. The script saves the `.blend` and prints the camera and the action keys before it renders.
