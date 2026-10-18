# Editron video knowledge base (for Grok Build)

**Purpose.** This repo teaches **Grok Build**, a coding agent that edits Daniel Klabunde's videos directly in **DaVinci Resolve** (scripting API, timelines, colour nodes), how Daniel wants his edits made. It is compiled from the real working notes of the earlier edits: the editing style guide, the grade notes, every project `PROGRESS.md`, the Zaidi brand notes, the approved caption scripts and the Chainlink edit brief. Nothing here is invented. Where something is unknown or only inferred, it is marked **UNKNOWN** or **ASSUMPTION**.

Clients covered: **Zaidi Jewelers** (a jewelry store launch campaign: vertical Reels) and **Chainlink** (Daniel's nightlife CRM app: talking-head promos with Daniel and Devon).

## Reading order
1. `shot-selection.md`: **the most important file.** Every shot rule Daniel has given, plus the checklist.
2. `workflow-and-qc.md`: how a job runs, the feedback history (each correction and the rule it became), frame-exact cutting, the pre-delivery QC and how to deliver.
3. `clients/zaidi.md` or `clients/chainlink.md`: client-specific rules (brand, captions, pacing, music).
4. `color-grading.md`: Daniel's three measured Zaidi grades and which look to use. These are pixel estimates. **Learn from his real Resolve node trees once you have them.**
5. `audio.md`: music start, loudness, SFX, ADR/lip-sync swaps, phone EQ, dereverb.
6. `resolve-notes.md`: how to translate all of this into Resolve. Read it before writing any Resolve script.
7. `luts/`: the three fitted `.cube` LUTs.
8. `LEARNINGS.md`: the live session log from Resolve (RH-4 on 18 Oct 2026, including his real node tree). It is not a style rule. Promote a line into the file that owns the topic only after Daniel confirms it. In the local skill those style files live under `references/`; in this repo they are the files at the root.

## Standing preferences (apply to every job)
- **Footage** lives on Daniel's **T7 Shield** external drive, organised by client (e.g. `/Volumes/T7 Shield/Client footage/Zaidi/Zaidi 2/Videos/C0xxx.MP4`).
- **Scripts** live in **Google Keep ("the Keep")** or **Notion** (Zaidi shotlists are Notion rows).
- **Everything is vertical.** Footage is shot vertical and the edits are fully vertical (1080×1920 deliverables; 4K masters are 2160×3840). Some Zaidi clips are recorded sideways and need rotating (ffmpeg `transpose=2`).
- **Daniel grades himself.** Build should learn from his graded files and, once available, his Resolve node trees, rather than inventing looks.
- **No repeated B-roll** in one edit, ever.
- **Something is always on screen / always happening.** No dead air, no empty establishing opener.
- **Full sentences stay on screen at least ~1.5 s** (45 frames at 30 fps).
- **Flag, don't hide.** Report every shot kept despite a concern, every rule that couldn't be met, and every assumption.

## How to use it
- Before an edit: read the client file plus `shot-selection.md`, then build a shot plan that passes the checklist.
- Before delivery: run the QC list in `workflow-and-qc.md`.
- When Daniel gives feedback: add it to the feedback history in `workflow-and-qc.md` as **note → general rule**, the same way the existing entries are written.
- Read `LEARNINGS.md` before trusting a fitted LUT or an assumed node tree. The log wins for what was measured. It does not replace a style file until he confirms the promotion.

## Provenance
Compiled Oct 18 2026 from `/workspace/editing-style-guide.md`, `/workspace/zaidi-grade-notes.md`, `zaidi4–8/PROGRESS.md`, `zaidi/adr/PROGRESS.md`, `chainlink-edit/c0141/PROGRESS.md`, `chainlink-edit/brand_brief.md`, `zaidi/brand.md`, the caption scripts (`zaidi/make_subs.py`, `zaidi/v7.py`, `zaidi2/build8/cap.py`, `zaidi4/build3/title.py`) and the Chainlink build scripts (`chainlink-edit/c0142/build.py`, `path.py`). Example stills were left out (the upload path only supports text files); the comparison images are listed by path in `color-grading.md`.
