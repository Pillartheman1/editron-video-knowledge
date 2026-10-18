# Editron learnings

Living log of what was confirmed while cutting **Zaidi Reel RH-4** inside Daniel's real DaVinci Resolve, 18 Oct 2026. This is the file to send back to https://github.com/Pillartheman1/editron-video-knowledge so Grok Bot can learn it.

This file is not a style rule. The style still lives in `references/`. A line moves there only after Daniel confirms it. Entries he has not confirmed are marked **NOT A RULE**. Things not yet seen in the viewer, the log, or the project database are marked **ASSUMPTION** or **UNKNOWN**.

The machine-readable cut is `jobs/Zaidi editron test__RH-4.json`. That file matches section 13. Earlier sections keep the cut they measured.

Append to this file in the same pass a fact is learned. Do not wait for the end of the job.

## How a fact got here

Confirmed means one of: the Resolve viewer matched a known still, the inspector showed the number, the project log showed relink 0 with no zstd or bounds errors, or a file measurement (mdhd, afinfo, MSE) matched the timeline. Screenshot descriptions from a vision model are not enough. They have misread rotation, timecode, and small gold type.

## 1. Which Resolve is the real one

Two apps are installed.

- **Studio**, the one that holds the project: bundle id `com.blackmagic-design.DaVinciResolve`, path `/Applications/DaVinci Resolve/DaVinci Resolve.app`, version **21.0.0b.0020** (public beta).
- **Lite**, the wrong one: bundle id `com.blackmagic-design.DaVinciResolveLite`, path `/Applications/DaVinci Resolve.app`.

`open -a "DaVinci Resolve"` launches Lite. Open Studio with:

```bash
open "/Applications/DaVinci Resolve/DaVinci Resolve.app"
```

Quit Studio with:

```bash
osascript -e 'tell application id "com.blackmagic-design.DaVinciResolve" to quit'
```

That call returns `User canceled. (-128)` every time. The process still exits and **saves**. Wait until `pgrep -f "/Applications/DaVinci Resolve/DaVinci Resolve.app/Contents/MacOS/Resolve"` is empty and `Project.db-wal` is gone before writing the database. Do not kill Resolve mid-write.

`IsProjectLiveSaveEnabled` is true. `AutoReloadPrevProj` is true. `LastWorkingProject` was `Zaidi editron test`.

Screen on this machine is 3420×2224. Capture with `screencapture -x`. `sips -c` is height then width. `sips --cropOffset` is vertical then horizontal. Quartz and pyobjc are not installed. System Events menus fail (`-1719`). System Events keystrokes fail (`1002`, osascript is not allowed to send keystrokes). The playhead cannot be moved from a script.

Crops that landed on 18 Oct 2026:

- Viewer: `sips -c 1100 900 --cropOffset 80 1500`
- Inspector: `sips -c 900 800 --cropOffset 80 2500`
- Timeline filmstrip plus the music waveform: `sips -c 420 3200 --cropOffset 1680 80`
- Pool, left rolls: `sips -c 900 1200 --cropOffset 40 0`
- Pool, right side (end card, timeline icon, song waveform): `sips -c 220 700 --cropOffset 160 700`

ffmpeg is `~/bin/ffmpeg`. There is no ffprobe. numpy is installed. librosa, cv2, and PIL are not. `zstandard` 0.25 is installed for the user python. `rg` is not on `PATH`. zsh `status` is read-only.

## 2. Scripting does not connect. The project database does.

`scriptapp("Resolve")` returns None. A Fusion script probe printed `RESOLVE_NIL`. TCP 1144 is not listening. `System.Scripting.Mode` is already 1 (Local). The skill says to stop there. Daniel overrode that: the same conditions have worked before, and the working method is to quit Studio and edit the project SQLite file.

Project library:

```text
~/Library/Application Support/Blackmagic Design/DaVinci Resolve/Resolve Project Library/Resolve Projects/Users/guest/Projects/<name>/Project.db
```

Log: `~/Library/Application Support/Blackmagic Design/DaVinci Resolve/logs/davinci_resolve.log`

Prefs: `~/Library/Preferences/Blackmagic Design/DaVinci Resolve/config.user.xml`

Rules for a write:

- Refuse to run if Studio is running or a `-wal` file exists.
- Copy `Project.db` to a backup first.
- `PRAGMA foreign_keys=OFF` during the edit.
- Page size is 4096. `chmod 666` the installed file after the write. A `VACUUM` rewrites the file and drops that mode.
- Commit, then `VACUUM` when a path string was replaced or a row was deleted. Old paths left in free pages have made relink pick up dead media.
- Round-trip every zstd blob: decompress, patch, compress, decompress again, and compare to the patched bytes.
- Read live row ids before writing. Do not reuse ids from an older backup. The ids in this file were true after the rh4_v1 install on 18 Oct 2026.

A clean open looks like this in the log: `Loading project (Zaidi editron test) ... took` a few hundred milliseconds, `Media pool relink status changed to 0`, `Current timeline pointer (RH-4) is changed`. Count of `Src size is incorrect` is 0. Count of `outside clip bounds` is 0. Relink status 1 means the media is broken.

These startup lines are normal and are not a failed open: `Create Decoder ... gets err: -12906` for mx5p/dvh3/ai12/xalg/xfi8/he22/AVdn, and the ProRes RAW plug-in directory error.

Do not modify `Fusion test`, `test graphic`, `Zaidi paper graded`, `Zaidi 6`, or any other `Project.db`. Zaidi 6 is a read-only template for blob layout. Do not clone a new project over `Zaidi editron test`. Keep project id `764f8fcf-1dc5-4bb3-b09c-2b43515b5980`.

`SM_Setup` has 0 rows. No "Scaling" string was found in the project or config blobs. **ASSUMPTION:** input scaling behaves as scale-full-frame-with-crop (cover). The viewer math below matched that assumption. Do not guess a different mode.

Colour science was inherited from Zaidi 6. **ASSUMPTION:** DaVinci YRGB, not colour-managed. Do not change it after media is in the project. The project metadata row still says 1920×1080. That is not the timeline resolution.

`SM_Project.FieldsBlob` `ThumbnailClipSet` still names some deleted pre-rendered mov ids. The project opened anyway. Leave that blob alone unless a load error names it.

## 3. The open project

| Thing | Id or value |
|---|---|
| Project | `Zaidi editron test`, `764f8fcf-1dc5-4bb3-b09c-2b43515b5980` |
| Timeline | `RH-4`, `b7e4b19a-761d-4fad-a924-2ac37ecc1234` |
| Sequence | `5ed778eb-3b1f-4e9d-8500-d4ff9c511dbe` |
| Master folder | `9f48fe2f-0241-4845-b0e3-0e0875525337` |
| Video track V1 | `67e8a16f-9e62-40d2-89e3-ffba00715833`, type 0 |
| Audio track A1 | `e2557e94-9bec-4798-a4b4-27e927833709`, type 1, flags 2, empty, muted |
| Audio track A2 | `9f4466eb-2904-4de2-8e67-dd248c040caa`, music |
| Timeline pool item | `332043a6-bdbb-4bf4-a0af-4b874be5a3cb` |
| First record frame | `107892` = `01:00:00:00` |

Timeline rate is 30000/1001. Resolution is 1080×1920. Plan in frames. `t = n × 1001/30000`. Never mix this with 30 fps.

Sequence `MediaExtents` is two little-endian doubles: start seconds, then **duration** seconds. The first double stays `3599.9964`. The second is `frames × 1001/30000`. Writing the end timestamp into the second double is wrong. After the draft install the duration is 635 frames = 21.187833 s. The viewer showed that as duration `00:00:21:05` (21×30+5 timecode frames).

Sequence `FrameRate` is 16 bytes: little-endian double `29.97002997002997`, then flag `01`. Sequence `Resolution` is two big-endian u64: `0x438` = 1080, `0x780` = 1920.

`Start`, `Duration`, and `In` on `Sm2TiItem` are text. Quote `"In"` in SQL. `In` is the source offset in timeline frames. `MediaStartTime` stays 0.

Track order is the junction `Sm2TiItem_Sm2TiTrack`: owner is the track id, associate is the item id, property `Items`, `DbIndex` is 0..n-1 in record order. Adding items means inserting those rows. The first ten camera items were updated in place. Six new items were inserted at indexes 10–15.

A1 stays an empty muted embedded-audio track. Camera audio is not a timeline item. Music stays on A2.

## 4. These camera files are sideways. This transform stands them up.

All five rolls are 3840×2160 landscape, H.264 High, yuv420p, tv, bt709, SAR 1:1, 29.97, no rotation tag. The camera was on its side. In the unrotated frame her head is on the right. ffmpeg `transpose=2` (90° counter-clockwise) stands her up.

The Inspector transform that matched `transpose=2` and filled the 1080×1920 frame, confirmed on the viewer:

- Rotation Angle **+90** (displayed `90.000`)
- Zoom X and Y **0.5625** (displayed `0.563`)
- Position 0, 0

**+90, not −90.** Negative 90 put her head at the bottom.

Why the zoom is 9/16: Resolve cover-scales the unrotated 3840×2160 into 1080×1920, which is a center slice (her chest). Zoom 16/9 made that worse. Zoom `0.5625 × cover = 0.5`, and after the +90 rotation the whole upright picture lands on 1080×1920 with no crop and no upscale. Side black in the viewer panel is the 9:16 frame sitting in a wide viewer.

Pool thumbnails stay sideways. The source viewer stays sideways. Rotation is stored on the timeline item, not on the media. Zaidi 6 has both +90 and −90 on one shared clip. Clip-attributes rotation was **not** found. If he resets the transform, the shot goes sideways and center-cropped again.

The transform blob is `Sm2TiItem.EffectFiltersBA`. Decompressed length 332. Protobuf parameter slots:

- id 42 Zoom X, id 43 Zoom Y, each a field-2 double, four copies of the zoom
- id 47 rotation, two copies of the angle
- ids 40 and 41 position, 0
- ids 45, 46, 48, 49 other channels, 0

The Zaidi 6 template value was zoom `0.7099999952316285` and rotation −90. Zaidi 6 zooms of 0.60–0.78 are punch-ins on top of the 0.5625 full-frame base. Do not copy 0.71 onto these files.

Wrapper for this blob and for `FieldsBlob`: big-endian u32 version 2, big-endian u32 size of (`0x81` + the zstd frame), the byte `0x81`, then a standard zstd frame. `zstandard` 0.25 frames are accepted by Resolve 21. `frame_content_size` must equal the raw length.

An end card that is already 1080×1920 and upright uses rotation **0** and zoom **1**. The +90 / 0.5625 pair on that file is wrong. Its geometry blob must say 1080×1920. Camera geometry stays 3840×2160.

## 5. Full files in the pool. Cuts are in-points.

Daniel's correction: do not import pre-rendered trims. Put the full camera MP4 in the media pool and set `In` and duration on the timeline item, so he can change the selection in the editor.

The abandoned trims are still on disk at `/Users/dan/Movies/editron/RH-4/01_open.mov` through `10_bench.mov`. Nothing in the project points at them. Do not point the timeline back at them. Do not re-run `rebuild_full_files.py`, `build_project.py`, or `render_shots.py`. Those scripts still expect the old mov names and `In` = 0, and a re-run mints new uuids.

`~/bin/ffmpeg` cannot be probed with `afinfo` on these MP4s (`AudioFileOpenURL failed`). Read `mdhd`. `mdhd` is stored before `hdlr`. Pair them per trak. A walker that assigns the next mdhd to the previous handler will report the audio timescale as the video timescale.

Measured from `mdhd` (video ticks at 30000, one frame = 1001 ticks, audio PCM at 48000):

| File | Frames | Seconds | Audio samples |
|---|---|---|---|
| C0133.MP4 | 4035 | 134.6345 | 6462480 |
| C0134.MP4 | 210 | 7.007 | 336480 |
| C0135.MP4 | 5265 | 175.6755 | 8432640 |
| C0128.MP4 | 600 | 20.02 | 960960 |
| C0129.MP4 | 240 | 8.008 | 384480 |

Footage directory, 54 characters:

```text
/Volumes/T7 Shield/Client footage/Zaidi/Zaidi 2/Videos
```

Filenames are 9 characters (`C0133.MP4`).

### Adding a roll

Clone a live camera row from **this** project (C0133), not C0061 from Zaidi 6. The live row already has the right directory, the big-endian audio uuid, and timecode `00:00:00:00`.

Mint new ids for `Sm2MpMedia`, `BtVideoInfo`, `BtAudioInfo`, and `UniqueMediaPoolItemId`. Set `pLmVerTable` to null. `AudioSource` stays `AUDIO_SOURCE_EMBEDDED`. The `EmbeddedAudio` column on `Sm2MpMedia` stays null. The link is the junction `BtAudioInfo_Sm2MpMedia`, property `EmbeddedAudioVec`, owner = media id, associate = audio id, index 0. Picture links through `Sm2MpMedia.Video`. `BtVideoInfo_Sm2MpMedia` had 0 rows.

Folder membership is `Sm2MpFolder_Sm2MpMedia`, property `MediaVec`. On 18 Oct 2026 the timeline sat at index 7, the three original rolls at 110–112, music at 200, and the new rows at 113 (C0129), 114 (C0128), 115 (end card). Check that the index is free before inserting.

Then patch, in order:

1. Clip path blobs (below).
2. `NumFrames` and timecode.
3. Audio sample count.
4. `FieldsBlob` duration and the embedded-audio uuid.
5. Geometry, only if the file is not 3840×2160.

### Clip path blob

`BtVideoInfo.Clip` and `BtAudioInfo.Clip`: big-endian u32 version 2, big-endian u32 payload size, byte `0x81`, then a one-block raw zstd frame. Magic at offset 9 is `28 b5 2f fd`. Byte 13 descriptor `0x20` means a 1-byte content size. Byte 14 is that size. Bytes 15–17 are a 3-byte little-endian raw-block header. The protobuf payload starts at offset 18.

The payload must stay under 256 bytes. A longer path needs the file copied to a short path. Do not hand-write a 2-byte frame-content size.

After any splice that changes length, rewrite the outer size, byte 14, and the block header (`fix_clip_frame`). The error `Src size is incorrect` is this bug.

Protobuf fields that matter: 1 directory, 2 filename, 3 date string, 5 codec (`avc1` or `Linear PCM`), 13 file-identity varint, 15 type, 16 varint 100, 18 varint. The video clip stores the filename twice and the directory once. The audio clip stores each once.

Stale dates still linked. A copied file-identity varint (field 13) still linked, including when several clones shared it. Leave field 13 alone unless the new varint is the same byte length.

**If the old string and the new string are equal, do not splice.** A search-and-replace that writes the same bytes back finds them again and loops forever. That hung the first rh4_v1 installer on C0129, whose directory was already correct. The process was killed before commit. The database was unchanged.

### NumFrames, timecode, extents

`BtVideoInfo.Time` strings are UTF-16-BE. A UTF-16-LE search hits one byte later and corrupts the string. Replace the aligned BE form only.

`NumFrames` is UTF-16-BE. Layout: type u32 `0x00000002`, one `0x00` pad, then the count as big-endian u32 at `name_end+5`. The next u32 must stay `0x12` (the `FrameRate` name length). Writing the count at `name_end+4` made Resolve report clip bounds `[0, 79]` and `[0, 39]`.

Timecode on the full rolls is `00:00:00:00`. `StartFrame` is 0.

Camera `FieldsBlob` names and the audio uuid are UTF-16-BE, twice. Each uuid is preceded by a big-endian length `0x0048`. That `0x48` is the length, not the letter H. The LE search is a false positive at `BE_offset+1`. Replacing it corrupts the BE uuid. Replace the full BE uuid, and only when it occurs twice.

`MediaExtents` inside that blob: BE name, type u32 `0x0000000c`, four zero bytes, byte `0x10`, then two little-endian doubles at `name_end+9` and `name_end+17`. Write `0.0` and the real duration in seconds. The SQL `MediaExtents` column is null. Another double near offset 197 (value 133184) is not a duration. Do not global-replace a duration just because the bits match somewhere else.

Audio `TracksBA` `Duration`: UTF-16-BE name, type u32 `0x00000004`, sample count big-endian u32 at `name_end+9`, and the next name-length u32 `0x0c` at `name_end+13`. Patch those four bytes. Do not resize the blob. A walker that splits on interior name matches will report the wrong count. Trust `name_end+9`.

## 6. Music link

The offline music icon was `Sm2MpMedia.FieldsBlob` still naming Zaidi 6's audio id `18621c4b-9885-47fb-b2fb-779b3d30604d`.

This project's music audio id is `84da11d3-d7ee-42de-a3aa-13dc187219c0`. Media id `edd157e2-8db6-4f17-ba31-656a2998ca51`. Timeline item `6a17bc0f-1520-4e44-8e7d-505e04f3b244`.

The music `FieldsBlob` is the opposite endianness of a camera blob. The uuid is really UTF-16-LE (it starts `64 00`, not `00 64`). Do not run the camera BE replace on it.

There are two MediaRef copies. The first is a clean UTF-16-LE uuid. The second is the same id except the last UTF-16 byte is `0x12`, not `0x00`. Keep that trailing `0x12`. After every music rewrite, check that both copies still name `84da11d3`.

`TracksBA` sample count and the `MediaExtents` duration double must describe the file the clip blob points at. A 15-second item pointed at a 15-second excerpt will read off the end if the timeline is 21 seconds.

The pool tile for a linked song is a green waveform. A2 is a green waveform for the whole timeline. The music item's `EffectFiltersBA` is a 38-byte blob, not null. Copying that blob onto a short WAV made Fairlight delete the clips. See section 12.

Resolve stores the priming-inclusive MP3 sample count. `afinfo` on the full Lissvik file:

```text
9924000 valid frames + 576 priming + 1056 remainder = 9925632
```

That is 206.784 s at 48000 Hz. The file is `/Users/dan/Music/ES_Bistro Barfing (Dan Lissvik Rework) - Dan Lissvik.mp3`, about 6.4 MB, ~260 kbps.

The full filename does not fit in the 255-byte clip payload. The working copy is `/Users/dan/Movies/editron/RH-4/song.mp3`. The clip blob keeps the short directory `/Users/dan/Movies/editron/RH-4` and the filename was spliced from `music.mp3` to `song.mp3`.

The old excerpt `/Users/dan/Movies/editron/RH-4/music.mp3` is 15.024 s (721152 priming-inclusive samples), cut from 23.947 s, single-pass loudnorm, 0.4 s fade. It is the wrong length and the wrong start for the draft. Leave the file on disk. Do not point the pool at it.

Two-pass `loudnorm` with `measured_*` and `linear=true` / `linear=false` wrote a full-size MP3 of digital silence. Do not restore that file. Single-pass `loudnorm=I=-14:TP=-1:LRA=11` with `-ss` before `-i` is audible. The draft does not use it.

**ASSUMPTION:** music `In` is timeline frames, because the item's `MediaFrameRate` is 29.97 and a Zaidi 6 music item stored `In` as `"614"`. It was not re-tested as a sample offset. If the waveform is the quiet intro, slip `In` by one frame. `19.1 × 48000 = 916800` samples if a sample offset is ever required.

## 7. Matching a picture to a source frame

Build a 36×64 RGB index of each roll, upright:

```bash
ffmpeg -i ROLL.MP4 -vf "transpose=2,scale=36:64:flags=area" -f rawvideo -pix_fmt rgb24 ROLL.rgb
```

One frame is 6912 bytes. Compare a draft frame scaled the same way. Mean squared error on 0–255 was 0.28–0.49 when the in-point was right, and hundreds or thousands when it was a different take.

Zooms tried on the match: 1.0, 1.25, 1.5, 1.8, 2.2, 2.8, 3.5. Zoom 1.0 of the full upright frame won every real shot. The draft does not punch in. The "close-ups" are how the camera was framed.

Verify the **first** frame of a shot, not only the middle. A mid-frame search put the lantern walk on the wrong take (first-frame MSE about 1564). The scene filter `gt(scene,0.25)` missed that cut because the two takes look alike. Check a frame on each side of any cut the filter might have swallowed.

`ffmpeg -ss` before `-i` selects the first frame whose PTS is at or after `t`. Frame index from a timestamp: smallest `n` with `n × 1001/30000 >= t`, which is `ceil(t × 30000/1001 - 1e-9)`. If a timeline still does not match the still, slip `In` by one frame. Do not go back to pre-rendered trims.

Do not pipe ffmpeg's banner into a Python heredoc. Write stderr to a file, then parse the file.

## 8. RH-4 picture and sound, as first installed

**NOT A RULE.** This is the draft replica from earlier on 18 Oct 2026. The same day he recut it. The picture he actually wants is section 11. This section stays so the diff has a before.

Daniel supplied `/Users/dan/Downloads/rh4_v1.mp4` and asked for that draft, shot for shot, using the lessons above. Do not write these choices into `references/` until he does.

The reference Reel is the LOHR Jewellery Instagram post `Da1ak2KzBKt`. Local copy `/tmp/editron-rh4/reference.mp4` (22.03 s, 720×1280, 30 fps). Their caption, their logo, and their name stay off screen. The CDN url expires.

The Notion shotlist is the RH-4 page. "This but at Cake Bake or something, then around city center and Palladium." Props: books, newspaper, spoon. Five collection rows. Row 4 "pelladium" means the Palladium steps. Row 5 is empty. The style guide says a "misc" row is **UNKNOWN**. This brief explicitly wanted the cafe beats.

The draft is 635 frames, 1080×1920, 29.97, H.264, AAC. Grok Bot rendered it with ffmpeg, not Resolve. No captions except the end card. Small-frame MSE against the ungraded source is 0.3–0.5, so the draft is the camera picture, not zaidi5 or zaidi8. Those cubes stay off the timeline. Daniel grades finals.

Record times are offsets from `01:00:00:00`. Source `In` is a 0-based frame on the full file.

| Record | Frames | File | In | What it is |
|---|---|---|---|---|
| 0 | 57 | C0133 | 180 | Back view, walking away, cafe, cup, flowers |
| 57 | 77 | C0133 | 899 | Hands, rings, gold spoon |
| 134 | 30 | C0133 | 3761 | Bracelet, Leopold magazine |
| 164 | 30 | C0134 | 60 | Overhead, "on the Rise", necklace, spoon, cup |
| 194 | 34 | C0133 | 1798 | Drinking, pendant |
| 228 | 42 | C0135 | 108 | Lantern walk, first take |
| 270 | 46 | C0135 | 929 | Same angle, later take |
| 316 | 41 | C0133 | 1499 | JUNGHANS magazine on the white table |
| 357 | 33 | C0129 | 30 | Street, buildings, cars, flowers, no person |
| 390 | 27 | C0135 | 5095 | Tight profile, pendant |
| 417 | 27 | C0135 | 4975 | Chest, cup, rings |
| 444 | 33 | C0135 | 1723 | Fountain, back of the dress |
| 477 | 27 | C0135 | 4795 | Seated profile, cup |
| 504 | 23 | C0135 | 4870 | Tighter seated cup, chin and rings |
| 527 | 40 | C0135 | 4316 | Sitting on the bench, Linden, foam finger |
| 567 | 68 | endcard.mov | 0 | Gold zaidi / Jewelers on black |

Durations sum to 635. Last record frame is `108527`.

The hidden cut is draft frame 270. Draft frames 228–269 are C0135 frames 108–149. Frame 270 jumps to C0135 frame 929 and stays continuous through frame 974. Do not merge those two items.

`C0128` is in the pool and matches nothing in the draft (best MSE was thousands). It is there so he can use it. It is not on the timeline.

Readable text was left in frame because the match is the full frame: Leopold, "on the Rise", JUNGHANS, Linden, spoon text, street signs and plates. JUNGHANS is upside down on the table in the draft still and in the viewer. That is how the magazine was lying.

No logo file was found under the Zaidi footage folders, Desktop, Documents, Movies, or the skill. Do not draw a new one. The end card is draft frames 567–634, extracted to `/Users/dan/Movies/editron/RH-4/endcard.mov` (1080×1920, avc1, 68 frames, geometry 1080×1920, rotation 0, zoom 1). The pool thumb shows the gold word. The style guide says end on a person and do not invent a logo. This card is the one in his draft, so it was replicated and flagged.

Building that mov: `-frames:v 68` together with `-shortest` left the PCM track at 108544 samples (2.261 s), short of 68 frames. A 68-frame 29.97 picture needs at least 108909 audio samples if something uses the shorter stream as the media length. The file that opened was video copied through and `pcm_s16be` (`twos`, same family as the camera "Linear PCM") padded with `-t 3` (144000 samples). The timeline item duration stays 68. **UNKNOWN:** a PNG still's database layout was never found. Use the mov.

Music on the installed timeline: full song, item duration 635, `In` = `"572"`. `572 × 1001/30000 = 19.085733` s, which is 14 ms earlier than the measured lock of **19.100 s** (correlation 0.999437 over the whole draft). Frame 573 is 19 ms late, so 572 is closer. The nearest measured beat is beat 57 at 19.255 s. Do not snap to it. He can slip the in-point.

The song's first half-second is nearly silent (rms about 0.003 at 16 kHz). At 19.1 s the rms is about 0.285. A loud waveform from the first timeline frame means the in-point is in the song. An earlier pass had called the tempo 179 BPM (beat period from 60/179, phase 0.148504 s) and parked the old cut at beat 71 = 23.947 s. The draft replaces that start.

No captions were added. A1 is empty. The deliverable is the open timeline. Do not render or post a file unless he asks.

Jobs snapshot `jobs/Zaidi editron test__RH-4.json` was overwritten with this cut and is the diff baseline. No feedback-history row was written. This install was the spec he handed over, not a correction of a previous rule.

### Flags kept on purpose

- Several shots are shorter than about two beats. The draft overrides the pacing rule.
- The end card overrides "end on a human moment" and "do not invent a logo."
- The picture is ungraded. The loudness rule (−14 LUFS, true peak −1) is not applied, because the draft is a straight excerpt.
- Cafe-through-zaidi8 reads cold. It is not on this timeline.
- Magazine and store text is visible. It was not cropped out.

## 9. Do not repeat these

- Launching Lite with `open -a "DaVinci Resolve"`.
- Retrying `scriptapp` after it has returned None on this machine.
- Rotation −90, zoom 16/9, or the template zoom 0.71 on these sideways rolls.
- Importing pre-cut movies when he asked for full files.
- Writing `NumFrames` at `name_end+4`.
- Replacing a UTF-16-BE uuid or timecode with a LE search.
- Putting the timeline end time in the second sequence `MediaExtents` double.
- Setting camera geometry to 1080×1920.
- Splicing a blob string onto itself.
- Two-pass loudnorm.
- Trusting one mid-frame match, or a scene threshold of 0.25, when two takes look alike.
- Trusting a vision description of rotation, timecode, or a tiny logo.
- Re-running the stale build scripts named in section 5.
- A home-directory `find` for a logo. It does not finish, and it did not find one.
- The three Foley cues from section 12. He rejected them and deleted the timeline items.

## 10. Still open

- Whether music `In` is frames or samples. See section 6.
- The real input-scaling setting. The cover math is empirical.
- His colour science. **ASSUMPTION:** DaVinci YRGB. His real node trees for this reel are section 13. The fitted cubes remain estimates.
- Where clip-attributes rotation would live, if it exists. The item transform is what the viewer used.
- How to park the playhead from outside the app. Keystrokes are blocked. A playhead column was not identified.
- Whether the end-card file-identity varint or the stale codec date will matter on a relink after the file is replaced. They did not matter on the open that followed the install.
- `C0128` is available and unused.
- The Instagram CDN url for the LOHR reference expires. Use the local copy.
- When he changes this timeline, snapshot again, diff against the jobs JSON, append the lesson here, and promote it into the one reference that owns the topic only after he confirms it.

## 11. His recut of RH-4, 18 Oct 2026

He changed the open timeline after the draft replica. This is his cut, read from the project while Studio was open, then checked against stills of every visible shot. It is **not** promoted into `references/`. One reel is not a new standing rule. The jobs file was updated to this cut so the next diff starts here.

The timeline is 751 frames (25.06 s), up from 635 (21.19 s). Music `In` is still frame 572 (19.086 s into the song). He lengthened the music item to 751 frames and did not move the song. On this pass the picture was recut against a fixed music start.

He added two video tracks. `VideoTrackVec` index 0 is V1 (the original track, bottom), index 1 is V2, index 2 is V3 (top). Where tracks overlap, the higher track is the picture. Lower clips run underneath and are not part of the story. Read the visible list below, not the raw item starts.

### What the story became

The draft was a montage: a back-view walk, then jewelry details, then a city montage of close-ups, then a bench, then the logo. He turned it into one evening with the cup as the thread.

Cafe, in order:

1. She is already seated at the table. Face, pendant, black dress. She reaches for the cup. Flowers in the foreground. This replaced the back-view walk. The film starts on her, doing something.
2. Her hands lift the lid. Rings and nails are the subject. The action finishes (lid off, coffee visible) inside the shot.
3. She stirs with the gold spoon. He punched in so the spoon, the foam, and the rings fill the frame.
4. She drinks. Profile, face and pendant, cup at her mouth. Full frame.
5. Overhead. The cup is empty in her hand, the gold spoon is on the table, the necklace is on the magazine ("on the Rise"). The drink is over. Short.
6. She reads the open magazine. Face, pendant, JUNGHANS in her hands, the cup at the edge. The magazine is something she is reading, not a still life on the table.
7. Her hand lifts the next magazine. Rings in the foreground.
8. The page opens onto a jewelry spread ("Gems on the Rise", a bracelet photo). The turn lands on the product.
9. The drink again, much tighter: cup, bracelet, and the hand. Same gesture as shot 4, a different frame. He kept the repeat because the second one is the jewelry.

City, still carrying the cup:

10. She drinks in the square, seen from the side and back, storefronts behind her.
11. She walks past the lantern. The cup is out of frame at the cut and in her hand by the end of the shot. He swapped the two lantern takes: the square drink (the later take) now comes before the walk (the earlier take).
12. The empty street from C0129, same frames as the draft (source 30, 33 frames). No person. It is a place cut in the middle, after she has been established, not an opener.
13. Foreground arm, gold bracelet, rings, the cup, fountain behind. The jewelry is on the hand that holds the cup.
14. She stands in front of the fountain and looks back at the camera. Full figure, the place still readable. He tightened it so she is larger than the camera wide.
15. She sits down on the bench. The shot is the action of sitting, from the side, not a posed close-up.
16. She is on the bench in front of the shop window, cup in hand, and crosses her legs. He punched in hard. Her figure, the cup, and the window are the frame. The Linden awning and the foam finger fall outside that punch.
17. The zaidi / Jewelers card. He kept it after recutting everything else, and shortened the visible hold to 57 frames (1.90 s).

He dropped the draft's isolated product shots: the Leopold cuff on a closed magazine, the JUNGHANS cover lying on the table, the tight face, the tight chest-and-cup, the chin-and-rings seated detail, and the back-of-dress fountain wide. Jewelry now shows up on a hand that is doing something (lid, spoon, page, cup) or on her in a place (fountain, bench).

`C0128` is still unused.

### Visible cut

Record offsets are frames from `01:00:00:00`. Zoom `0.5625` is the full upright frame. A higher zoom is his punch-in on top of that. Rotation stays +90 on every camera shot. Position values of about −0.05 to −0.08 are stored on three shots. In the same units the inspector uses for zoom, those are a fraction of a pixel, so they are treated as residue from the zoom control, not as a reframe.

| Rec | Frames | Sec | File | Source | Zoom | What he kept |
|---|---|---|---|---|---|---|
| 0 | 69 | 2.30 | C0133 | 246 | 0.64 | Seated at the cafe, reaches for the cup |
| 69 | 35 | 1.17 | C0133 | 923 | 0.5625 | Lifts the lid, rings |
| 104 | 49 | 1.63 | C0133 | 1195 | 0.86 | Stirs, gold spoon, rings |
| 153 | 33 | 1.10 | C0133 | 1799 | 0.5625 | Drinks, face and pendant |
| 186 | 25 | 0.83 | C0134 | 77 | 0.5625 | Overhead, empty cup, spoon, necklace |
| 211 | 47 | 1.57 | C0133 | 2407 | 0.63 | Reads the magazine |
| 258 | 35 | 1.17 | C0133 | 3151 | 0.61 | Lifts the next magazine, rings |
| 293 | 32 | 1.07 | C0133 | 3341 | 0.61 | Page opens on the jewelry spread |
| 325 | 63 | 2.10 | C0133 | 1786 | 0.90 | Same sip, tighter: cup and bracelet |
| 388 | 34 | 1.13 | C0135 | 940 | 0.5625 | Drinks in the square, from the side |
| 422 | 42 | 1.40 | C0135 | 108 | 0.5625 | Walks past the lantern, cup comes up |
| 464 | 33 | 1.10 | C0129 | 30 | 0.5625 | Street, no person |
| 497 | 43 | 1.43 | C0135 | 604 | 0.62 | Arm, bracelet, cup, fountain behind |
| 540 | 44 | 1.47 | C0135 | 1831 | 0.79 | Faces camera at the fountain |
| 584 | 41 | 1.37 | C0135 | 4692 | 0.65 | Sits down on the bench |
| 625 | 69 | 2.30 | C0135 | 4247 | 1.02 | On the bench, crosses her legs |
| 694 | 57 | 1.90 | endcard | 20 | 1 | zaidi / Jewelers |

### Timing

The cuts do not land on the 179 BPM grid. Distance to the nearest beat at that tempo runs from 4 ms to 168 ms, and most are 50–160 ms off. Two land close (the overhead, and the arm at the fountain). Do not snap this cut back onto that grid.

The pace follows the action. The opening sit and the closing bench are the long holds, both 2.30 s. The return to the cup is 2.10 s. The doing-something inserts sit around 1.1–1.6 s. The overhead is the short one, 0.83 s, and it still shows the empty cup, the spoon, and the necklace. The street plate stays 1.10 s. The logo hold is 1.90 s.

He cuts when the action has read: the lid is off, the page has reached the bracelet, she has sat down. The next shot is the result, not another angle of the same instant.

### Composition

Full frame (`0.5625`, rotation +90) is still the base. He punches in from that base when the subject should be bigger. He does not punch in on the lid, the first sip, the walk, or the street.

| Zoom | Use on this cut |
|---|---|
| 0.56–0.65 | The place and her whole figure. Opening sit, lid, first sip, overhead, reading, page turn, square, walk, street, sit-down. |
| 0.79–0.90 | She, or the hand, becomes the subject and the place stays. Fountain portrait. Stir. The tighter sip (cup and bracelet). |
| 1.02 | The bench master. She, the cup, and the shop window fill the vertical frame. |

### What to copy next time, and what not to freeze

Copy, until he says otherwise: start on her doing something, keep the cup as the thread through cafe then city, show jewelry on a hand that is acting, turn a prop into an action (she opens the magazine onto the jewelry), let a repeated gesture return only when the frame has changed, hold the open and the close longer than the inserts, leave the music in-point put and let the picture change around it, end on the zaidi card.

Do not yet write into the style files: "ignore beats," "repeats are fine," "always end on the logo," or "always punch in to 1.02." Those are what this one recut did. The beat rule, the no-repeat rule, and the human ending are still the written style until he confirms the change.

The next pass moved the wide sip, added zoom keyframes, and graded every camera shot. Those measurements are section 13. The line above that calls a position of about −0.05 a fraction of a pixel is corrected there.

## 12. Sound under his recut, 18 Oct 2026

**Rejected.** He said this pass was terrible. The cues are gone from the timeline. A3 is still there, named SFX, with no clips on it. The song is the only audio item (`In` 572, duration 751). `spoon.wav`, `page.wav`, and `sit.wav` are still in the media pool. Do not put the cues back. Do not copy the three-cue design, the −30 dB print, or "ignore the one-effect rule" into a later reel. The link notes in this section stayed true. The creative result did not. `references/audio.md` was not edited.

He asked for sound design, and for the research first. A deep-research pass kept 19 of 24 claims. The cues below were on A3. They were **not** a new house rule.

### What the research actually supports

The premix is dialogue, music, and effects. Effects work splits into hard (spot) effects, Foley, and ambience. Room tone is fill for dialogue edits, not the bed that builds the place. This reel has no dialogue. Source: Tomlinson Holman, *Sound for Film and Television*, 2nd ed., ch. 9.

Foley is body movement, footsteps on the right surface, and prop specifics such as a cup set down, performed to picture and often louder than life. Spot effects are one sound for one visible event, laid on the frame. An action inside the shot can be several effects. Sources: Hilary Wyatt and Tim Amyes, *Audio Post Production for Television and Film*, 3rd ed., ch. 12.

Walter Murch's limit is about two and a half things of one kind at once. Past that they cancel, and the track seems loud without being loud. The song is already the full layer. A hum, a walla, or a whoosh under it is a second layer in the same place. Source: Walter Murch, quoted via FilmSound.org.

Randy Thom: a music-driven sequence needs only enough sound to support reality. A later remark that loud music and loud effects are not played together was checked and rejected as a statement of that practice. Source: filmsound.org, "Designing a Movie for Sound."

In an all-music montage the music is often held steady and the effects drop out. If a few effects stay, the practical note is to make them punchier so they cut the score. No source gave a decibel offset for effects under music. Source: Jeff Shiffman, Boom Box Post.

"One subtle effect" is not a guild rule or a broadcast spec. Ordinary commercials are mixed louder and with less dynamic range than film. A product-video note only suggests quiet room tone, the original product sound, and one accent. Sources: the verified report's coverage of Holman, A Sound Effect, and RouteNote.

Michel Chion's synchresis: a sound and a picture that happen together weld, and the viewer credits the sound to the object. A wrong material still sticks. A glass clink on a paper lid would become the lid. Do not do that.

Production sound is kept when it beats a library effect, and dropped when it is noisy or muddy. The camera PCM on these sixteen shots was measured and dropped. Cafe shots sit around −14 to −19 dBFS RMS, crest only 11–15 dB, steady, often peaked near 125 Hz. The lantern walk is louder and tonal near 280 Hz, which is wind or handling, not footsteps. Laying that under the song would be a second low-frequency noise layer.

One checked claim was rejected and is not used here: that a score heard with no sync sound "reads as montage." Holman says a score heard alone *usually* distances the scene. That is not a rule that this reel must carry effects.

### What was added

Three Foley cues, one at a time, never stacked. Each is tied to one action in his recut. The files peak at **−30 dBFS**, which is the level he already approved for a paper rustle. The clip fader is at unity, so he can raise a cue without baking a new file. No loudnorm. No fade on the song. Music `In` is still 572. Music duration is still 751. Picture was not moved by this pass.

A3 is track `460ff56f-2a02-425b-aadd-fd06a9d3ebec`, name `SFX`, flags 0, not muted. A1 stays muted and empty. A2 stays the song.

| File | Record | Frames | Seconds | Action |
|---|---|---|---|---|
| spoon.wav | 134 | 4 | 0.160 | The spoon leaves the cup in the stir shot (the shot starts at record 104) |
| page.wav | 280 | 17 | 0.575 | The page is in the air at the end of the lift and settles on the jewelry spread |
| sit.wav | 592 | 14 | 0.500 | The move down onto the bench (the sit shot starts at record 584) |

The files are `/Users/dan/Movies/editron/RH-4/sfx/`, stereo, 48 kHz, 16-bit little-endian PCM.

- Spoon is BigSoundBank CC0, "Spoon into a Mug of Coffee" (s0410), one isolated tick. The recording is a mug. The cup in the shot is paper. **Flag.**
- Page is BigSoundBank CC0, "Turned Page" (s0164). It is a book page. The prop is a magazine. **Flag.**
- Sit is his own `/Users/dan/Music/Folly/Leather scrunch.mp3`, from 0.12 s for half a second. The bench is metal slats. Leather will not sound like metal. **Flag.**

The sit cue starts about 0.27 s into that shot and ends before the still where she is seated (about 28 frames in, record 612). It plays the move, not the moment she lands. Slip it later if the landing is the event he wants.

Left out on purpose: a lid (no paper or plastic lid file; a bottle clink would weld to the wrong object), footsteps, the fountain, room tone (the Epidemic "small room hum" on disk peaks near 90 Hz and is electrical), party walla, whooshes, an end-card hit, and the camera audio.

### The written rule this breaks

`audio.md` still says at most one effect, tied to an action, near −30 dB. Zaidi 5's note is "Paper SFX too much." He asked for sound design on this reel, so there are three cues. They stay quieter than the research's "make them punchier," because −30 dB is the level he has already signed off and the punchier note has no number. If a cue is inaudible on a phone, raise that clip. Do not bake a hotter file unless he asks. Do not promote "three cues" or "ignore the one-effect rule" into `references/` until he says so.

### How a standalone WAV gets online

Confirmed on the reopen after the patch. The pool showed `spoon.wav`, `page.wav`, and `sit.wav` with no offline slash. A3 drew them in green and the track header read "3 Clips." The log did not repeat `Invalid audio source reference and channel mapping` or `Faders Constraint Violated`. The song waveform was still the loud section of the track.

Cloning the song's `BtAudioInfo` is the wrong shape for a 16-bit WAV. The song row has a `ChannelLayout` field (type 2, one value byte `0x02` after four zero bytes) and a bit-depth value byte `0x03`. A camera PCM row that already links has no `ChannelLayout` and a bit-depth byte `0x01`. A 24-bit WAV that already links in the Moc project has no `ChannelLayout` and a bit-depth byte `0x02`. The header field-count byte at offset 30 is `0x0A` on the song and `0x09` on the linked PCM. The size word at offset 20 is `(blob length − 23) << 8`, which is `0x00019300` for a 426-byte PCM track.

The `ChannelLayout` block is 39 bytes: a 4-byte length, the UTF-16-BE name, type `0x00000002`, four zero bytes, and one value byte. Deleting those 39 bytes, setting the bit-depth byte to `0x01`, and writing the PCM header made the files link. `SampleRate`'s 12 bytes stay `00000003000000bb80000000`. The sample count stays at `Duration` name-end + 9.

The clip protobuf tail copied from the song ends `88 80 02` (field 17). A standalone WAV that links ends `80 80 02` (field 16, value 256) and keeps field 15 at `0x02`. Embedded camera PCM ends `80 80 01` and uses field 15 at `0x04`. The cues use the standalone-WAV tail. The path and the `Linear PCM` codec string stay.

That was not sufficient. Once the files opened, Fairlight logged `Faders Constraint Violated` and the next save deleted the three timeline items. The media rows stayed. The items were clones of the song item, so they still carried the song's effect blob, an `In` of `"0"`, a timemap of 177.47 s, and a duration rounded up past the end of the file.

A WAV item that already plays has `EffectFiltersBA` null, `In` null, and a timemap of file seconds minus one timeline frame (`0x02` plus an 8-byte big-endian double). The duration has to end inside the file: `floor(samples × 30000 / (48000 × 1001))`. That is 4, 17, and 14 frames for these three files. Restoring the items that way, and not touching the picture or the grades, survived the next open.

While this was being written he was in the project. Quitting Studio saved his color work. Sixteen clips now have a color version, including the opener. Those grades, the keyframes, and the sip he replaced are section 13. The inspector zoom 1.290 was the tight sip only.

## 13. Keyframes, the sip he split, and the grade, 18 Oct 2026

He rejected the sound pass, then changed the picture himself. This section is that change, read from the live `Project.db` while Studio was open. The file was 2863104 bytes, mtime 18 Oct 2026 17:57:56, no `-wal`. Picture, grades, and the song were left as he saved them. `references/` was not edited. **NOT A RULE.**

The song is still the only audio item (`In` 572, duration 751). A3 is still named SFX and still empty. The three wavs are still in the pool.

### How the visible picture was read

`Sm2TiItem_Sm2TiTrack.DbIndex` is the clip's order on its own track. It is not the layer. A script that used that index as the layer printed the wrong picture.

The layer is `Sm2SequenceContainer_Sm2TiTrack`, property `VideoTrackVec`. Index 0 is V1 (`67e8a16f`, bottom). Index 1 is V2 (`10e2cd8c`). Index 2 is V3 (`662e2d17`, top). The higher track is the picture. A clip's head can sit under the shot before it. Those hidden frames are in the item and are not in the story.

The grade join is `Sm2TiItem.pLmVerTable` → `ListMgt::LmVersionTable.pActive` → `ListMgt::LmVersion.Body`. Querying `LmVersionTable.Sm2TiItem_id` misses most clips. That column is set on two tables. The body is zstd. Magic `28 b5 2f fd`. Field 1 of the decompressed body is the node graph. Repeated field 7 is a node. Repeated field 8 is a link (field 1 source index, field 3 destination).

Each graded clip has its own version. A hash of the raw body changes per clip because node field 12 is a per-clip varint. Compare the float vectors, or the curve payload bytes. A body hash is not a grade fingerprint.

### The clip that was used twice

Section 11's wide drink and tight drink were the same frames. The wide visible window was C0133 source 1799–1831. That sat inside the tight window, source 1786–1848.

He replaced the wide item. New item `1d6b3cb6-0779-404a-add6-6ad1772687db`, record 147, duration 39, source in 1850, last source frame 1888, on V1. The stir on V2 covers the first 6 frames, so the viewer sees record 153–185, source **1856–1888**.

The tight item is still `3b4c83e5-2bf6-4656-b8f4-ec8d16c8a09f`, record 318, duration 70, source in 1779, last source frame 1848, on V2. V3 covers the first 7 frames. The viewer still sees record 325–387, source **1786–1848**. Those source frames did not move. The frame did: zoom 0.90 became 1.29, and tilt is −247 px.

The two visible ranges do not share a frame. The tight window ends at source 1848. The wide window starts at source 1856. Seven source frames sit between them and are not on screen. The tight insert plays later in the edit and earlier in the take.

Stills of the ungraded roll, stood up with `transpose=2`:

- Source 1850 is the item head, hidden under the stir. The cup is at her mouth. Bracelet, pendant, face.
- The first frame the viewer sees is source 1856, six frames later. That exact frame was not pulled. The cup is still at her mouth at 1850 and is down beside her face at 1869, so 1856 sits inside the sip.
- Source 1869: the cup is down beside her face, lips pursed, eyes down. Bracelet and pendant still read.
- Source 1888: the cup is out of frame. Eyes down, pendant and face. This is the reaction after the drink.
- Source 1779 is the tight item's head, hidden under the page turn. Wider: cup at her mouth, table and magazine in frame.
- Source 1829, inside the tight window: cup at her mouth, face, bracelet, pendant.

He split one take into consecutive parts of the action. The wide shot, played first, is the end of the sip. The tight shot, played later, is the cup-at-mouth jewelry, punched in. **NOT A RULE.** Section 11's "repeats are fine" does not survive this pass. "Never repeat a frame" is not the lesson either.

Three other item heads also slipped under the shot above them. The visible picture of those shots did not change:

| Item | Record | In | Hidden head | First visible source |
|---|---|---|---|---|
| Lid | 57 | 911 | 6 frames, under the opener | 923 |
| Tight sip | 318 | 1779 | 7 frames, under the page turn | 1786 |
| Square drink | 377 | 929 | 11 frames, under the tight sip | 940 |

### Visible cut after this pass

Record offsets are frames from `01:00:00:00`. Source ranges are the frames the viewer sees. Zoom `0.5625` is still the full upright frame. A keyframe pair is the zoom at the first key and the zoom at the last key inside the shot.

| Rec | Frames | Track | File | Visible source | Zoom | What it is |
|---|---|---|---|---|---|---|
| 0 | 69 | V2 | C0133 | 246–314 | 0.64 → 0.69 | Seated, reaches for the cup. Slow push-in. |
| 69 | 35 | V1 | C0133 | 923–957 | 0.6225 → 0.591 | Lifts the lid. Slight widen. |
| 104 | 49 | V2 | C0133 | 1195–1243 | 1.09 | Stirs. Pan −130 px, tilt −182 px. No zoom key. |
| 153 | 33 | V1 | C0133 | 1856–1888 | 0.5625 | End of the sip. Cup comes down. Full frame. |
| 186 | 25 | V1 | C0134 | 77–101 | 0.7625 → 0.6525 | Overhead. Widens. Pan −25 px on the last frame. |
| 211 | 47 | V1 | C0133 | 2407–2453 | 0.68 → 0.61 | Reads. Widens. |
| 258 | 35 | V3 | C0133 | 3151–3185 | 0.61 | Lifts the next magazine. |
| 293 | 32 | V3 | C0133 | 3341–3372 | 0.61 | Page opens on the jewelry spread. |
| 325 | 63 | V2 | C0133 | 1786–1848 | 1.29 | Cup-at-mouth jewelry. Tilt −247 px. No zoom key. |
| 388 | 34 | V1 | C0135 | 940–973 | 0.5625 | Drinks in the square. |
| 422 | 42 | V2 | C0135 | 108–149 | 0.5625 | Walks past the lantern. |
| 464 | 33 | V1 | C0129 | 30–62 | 0.6325 → 0.572 | Street. Widens toward the full frame. |
| 497 | 43 | V2 | C0135 | 604–646 | 0.62 | Arm, bracelet, cup, fountain. |
| 540 | 44 | V1 | C0135 | 1831–1874 | 0.79 | Faces camera at the fountain. |
| 584 | 41 | V1 | C0135 | 4692–4732 | 0.65 | Sits down. The item is 89 frames. The bench covers the tail. |
| 625 | 69 | V2 | C0135 | 4247–4315 | 1.10 → 1.02 | On the bench. Eases out. Pan held at −91 px. |
| 694 | 57 | V1 | endcard | 20–76 | 1.28 → ~1.16 | zaidi / Jewelers. Ungraded. |

Relative to the 0.5625 full-frame base, zoom 1.29 is 2.29×, the stir at 1.09 is 1.94×, and the bench starts at 1.96× and eases to 1.81×. The opener's push from 0.64 to 0.69 is a small move on top of an already tight frame.

### Keyframes are on the transform. Color is one number.

Wheel values and curve points are single stored numbers. Nothing in the grade is keyframed.

Transform keys live in `Sm2TiItem.EffectFiltersBA`, same version-2 zstd wrapper as section 4. Inside a parameter, field 10 is not protobuf. Layout: a little-endian int32 header, then records of `u32` source frame, `u32` flag, little-endian double. Zoom, pan, tilt, and rotate use flag `0x000c0000`. Flip and flop use `0x00340000`, and that stride leaves 8 bytes over. Ignore flip and flop. Slots: 42 Zoom X, 43 Zoom Y (always equal), 40 Pan, 41 Tilt, 47 Rotate. No extra ease doubles are stored. Do not call the ramp a bezier. A two-key header is `0xe0ffffff`. A four-key header is `0xc0ffffff`. A single held key is often `0xf0ffffff`.

Times are **source frames**. Record frame = `(source time − In) + record start`, and only when the time sits inside the item.

The static number stored beside the keys is the value in field 3 of the parameter. It matches the first key on the opener (0.64) and the bench (1.10). It does not match the keys on the lid (static 0.5625, keys 0.6225 → 0.591216), the overhead (static 0.5625, keys 0.7625 → 0.6525), the reading shot (static 0.63, keys 0.68 → 0.61), or the street (static 0.5625, keys 0.6325 → 0.571598). When a key's time is inside the item, that key is the picture. The static slot can be an older number he left behind.

Shots with no key inside the trim hold the static zoom: stir 1.09, wide sip 0.5625, magazine lift 0.61, page 0.61, tight sip 1.29, square 0.5625, lantern walk 0.5625, arm 0.62, fountain face 0.79, sit 0.65. The stored tight-sip zoom is the float `1.289999976`, which the inspector shows as 1.290. That number is this one shot.

A copied pair sits on several clips at source frames **2512** and **2607**. On the lid, the new sip, the overhead, the square drink, the walk, and the street, those keys are zoom 0.5625 and zoom 0.64. On the end card the same times store zoom 1 and zoom 0.64. Frame 2512 is a real C0133 frame (the roll is 4035 frames) and it is outside every trimmed item that carries it. C0134 is 210 frames, C0129 is 240, and the end card is 68, so on those files the times are past the media. A blob whose only zoom keys are this pair does not move. The pair looks like a dynamic-zoom leftover: 0.64 is the opener's start zoom, 0.5625 is the full frame, and 1 is the end card's old zoom. Leave it. Do not describe it as the shot's move.

### Position is a fraction of the frame

Section 11 called a position of about −0.05 a fraction of a pixel. That reading is wrong. After the +90 rotation, Pan × 1080 and Tilt × 1920 land on integer pixels of the 1080×1920 timeline.

| Shot | Stored | Pixels |
|---|---|---|
| Stir pan | −0.1203703704 | −130 |
| Stir tilt | −0.09479166667 | −182 |
| Tight sip tilt | −0.1286458333 | −247 |
| Bench pan | −0.08425925926 | −91 |
| Overhead pan, last frame only | −0.023148 | −25 |

These are reframes. Which screen edge a negative sign points at was not checked in the viewer on this pass. Do not label a sign as left or right until a still says so.

The stir's section 11 position (−0.0546) and the tight sip's section 11 tilt (−0.0536) are gone. He replaced them with the pixel offsets above. The bench pan was already about −0.0843 in section 11. It is −91 px.

### The end card upscales, and it reads past the file

The card is 1080×1920, 68 frames (0–67), rotation 0, no grade (`pLmVerTable` is null). Item record 687, duration 64, source in 13, last source frame 76. The bench covers record 687–693, so the viewer sees source 20–76.

Zoom keys: 1.28 at source 20 (the first visible frame, record 694) and 1.1636 at source 77. Source 77 is the first frame past the item and past the file. The visible card eases from 1.28 toward about 1.16. Both numbers are above 1, so the card is scaled up. The rotate key at source 2512 is the phantom pair, value 0. Rotation stays 0.

Flag both. Do not "fix" the upscale. Do not trim the over-read unless he asks. Section 4's "zoom 1 on an upright card" is what a fresh card needs. It is not what this item does now.

### The node tree on every camera shot

Sixteen camera clips, each with four nodes. The end card is the only picture item with no grade. There is no group grade and no shared node. He pasted the same tree onto each clip, then changed the wheels in two blocks.

Left to right, from the link list and from the node x positions:

1. Node 1, x = 190. Unlabeled corrector. Custom curves and hue curves. Tool family `0x86000000`.
2. Node 2, x = 516. Unlabeled corrector. Primary wheels. Tool family `0x06000000`.
3. Node 4, x = 758. Label `Glow`. Plugin `com.blackmagicdesign.resolvefx.glow`, context `OfxImageEffectContextFilter`.
4. Node 3, x = 1020. Label `Vignette`. Plugin `com.blackmagicdesign.resolvefx.vignette`, same context, mode `VIGNETTE_PLUGIN_MODE_ADVANCED`.

Links: node 1 → node 2 → Glow → Vignette. Curves run before the wheels. The glow and the vignette run after both.

No LUT string, no `.cube`, and no `zaidi` path anywhere in the bodies that were searched (the stir, the overhead, and the street). The fitted zaidi5 / zaidi7 / zaidi8 cubes are not on this timeline. They stay estimates in `references/color-grading.md`.

The OFX instance uuid differs per clip (the `_3` and `_4` suffixes). The plugin name is shared.

This is the tree he built. It replaces, for this reel only, the assumption in `references/resolve-notes.md` of an exposure trim, then a Daniel LUT, then a highlight-rolloff node, then a vignette on some shots. There is no LUT node. There is no separate rolloff node. The rolloff, if the curve reading below holds, lives inside node 1 and is then trimmed by the wheels. Do not write that replacement into `resolve-notes.md` until he confirms it.

### Two wheel presets, split by the setup

Resolve stores only the wheel values he moved. Each preset is exactly 12 floats, one array, path `/9/1/6/2/3/2/1`. The parameter ids, under base `0x06000000`:

`0x18, 0x19, 0x1a, 0x1b`, skip `0x1c`, `0x1d, 0x1e, 0x1f, 0x20`, skip `0x21`, `0x22, 0x23, 0x24, 0x25`.

The same gaps show up on Zaidi 6, Zaidi paper graded, and CT, which were read and not written. The skipped ids are absent whenever the quartet is saved. Gamma is absent, so gamma is at its default. Gain and gamma both default near 1, and the stored group near 1.0 is the one he moved. The skipped single in each wheel is **UNKNOWN**. A lum-mix left at default is a guess. It is not proven.

**ASSUMPTION** on the channel names. The file does not store them. The working labels are Y, R, G, B inside each quartet, and the quartets are Lift, Gain, Offset. The evidence: the first of each quartet carries the exposure; the three groups sit near 0, near 1, and near 0, which is how Lift, Gain, and Offset default; the third channel is the one he swings, which matches the written habit of pulling green and keeping skin rosy, and the same third-channel swing shows up on Zaidi 6 (third gain 0.946 on one preset, 1.242 on the other). Treat the names as labels for the table. Do not state them as stored facts.

Look A, on the opener, the lid, the stir, and the overhead (records 0, 57, 104, 186):

| Id | 0x18 | 0x19 | 0x1a | 0x1b |
|---|---|---|---|---|
| Lift-like | 0.01400 | −0.01716 | −0.11092 | −0.01276 |
| Id | 0x1d | 0x1e | 0x1f | 0x20 |
| Gain-like | 1.11245 | 1.04716 | 0.98524 | 1.05902 |
| Id | 0x22 | 0x23 | 0x24 | 0x25 |
| Offset-like | 0.06456 | 0.00588 | −0.05703 | 0.01682 |

Look B, on the new sip, the reading, both magazine shots, the tight sip, and every exterior including the street (records 147, 211, 258, 293, 318, 377, 422, 464, 497, 540, 584, 625):

| Id | 0x18 | 0x19 | 0x1a | 0x1b |
|---|---|---|---|---|
| Lift-like | −0.05253 | −0.01137 | −0.02383 | −0.01276 |
| Id | 0x1d | 0x1e | 0x1f | 0x20 |
| Gain-like | 1.15585 | 1.05211 | 1.00234 | 1.07578 |
| Id | 0x22 | 0x23 | 0x24 | 0x25 |
| Offset-like | 0.13991 | −0.01075 | −0.11308 | 0.01682 |

Two values are identical on both looks: the fourth lift (`0x1b` = −0.01276) and the fourth offset (`0x25` = 0.01682). The fourth gain is not identical (1.05902 vs 1.07578). He did not leave the B channel untouched.

The split is the setup, not the timeline order. Look A is the hands and the table, and it includes the overhead, which plays after the sip. Look B is the face shots in the cafe and every exterior, including the empty street. He graded by the kind of shot. He did not walk the timeline and trim exposure one clip at a time. This is two presets pasted onto clips, not one LUT plus tiny trims.

`log2(1.11245)` is about 0.15 stop and `log2(1.15585)` is about 0.21 stop, on the gain-like master term alone. The offset term is additive. It is not a stop. This is not the fitted zaidi5 "+1 stop," and it is not a measured stop for the whole image. No ΔE was computed from these nodes.

Qualitatively, Look A pulls the third channel hard in the lift (−0.111) and again in the offset (−0.057), with the third gain slightly under 1 (0.985). Look B is the brighter preset (gain-like 1.156, offset-like 0.140) and moves that third-channel subtraction out of the lift (−0.024) and into the offset (−0.113). Pulling the third channel down is the "take the green out, keep the skin rosy" direction. It is the opposite of the rejected Zaidi 8 v2/v3 grade, which lifted, cooled the whole frame, and desaturated. Do not describe this grade as a global cool. The fourth gain sitting above 1 (1.059 and 1.076) is consistent with taking yellow out of the whites. That sentence is an inference from the channel guess. It is not a measured white balance.

### The curve node

Node 1 has 20 saved parameters. Eleven of them are 2-byte setup values, field 2 as a varint. On the stir, and on the overhead which matches it:

| Id | 0xb7 | 0xb8 | 0xb9 | 0xba | 0xbb | 0xbd | 0xbe | 0xbf | 0xcf | 0x118 | 0x202 |
|---|---|---|---|---|---|---|---|---|---|---|---|
| Value | 0 | 0 | 0 | 4 | 0 | 0 | 0 | 0 | 2 | 2 | 0 |

`0xbc` is skipped. **UNKNOWN** what the menu calls these. They travel with the node. Do not invent control names.

Nine parameters hold points. Payload bytes for `0x400`–`0x405` match across every camera clip, including the street and the overhead. The float vector of the whole curve node matches the stir on records 0, 57, 104, 147, 211, 258, 293, 318, 377, 422, 497, 540, 584, and 625. The overhead matches those curve payloads and then adds the identity matrices below. The street matches `0x400`–`0x405` and changes only the hue curves.

`0x400`, `0x401`, and `0x402` are 16-point splines. X runs from about −0.5 to 2.0. Y on `0x400` stays between 0.484 and 0.498. Y on `0x401` runs about 0.36 to 0.56. Y on `0x402` stays about 0.42 to 0.47. A curve whose Y sits near 0.49 across an X range of −0.5 to 2 would grey the image if X were input and Y were output. They read as spline handles he copies with the node. Do not narrate them as "map this input to that output."

Shared points, four decimals, from the float32 values on the stir:

`0x400`: (−0.4867, 0.4978), (−0.2442, 0.4911), (−0.2441, 0.4844), (−0.0016, 0.4844), (0.2559, 0.4844), (0.2559, 0.4978), (0.5133, 0.4978), (0.7559, 0.4978), (0.7559, 0.4844), (0.9984, 0.4844), (1.2559, 0.4844), (1.2559, 0.4978), (1.5133, 0.4978), (1.7559, 0.4978), (1.7559, 0.4911), (1.9984, 0.4844).

`0x401`: (−0.4945, 0.5600), (−0.2718, 0.4658), (−0.2527, 0.3556), (−0.0109, 0.3556), (0.2473, 0.3556), (0.2473, 0.5600), (0.5055, 0.5600), (0.7473, 0.5600), (0.7473, 0.3556), (0.9891, 0.3556), (1.2473, 0.3556), (1.2473, 0.5600), (1.5055, 0.5600), (1.7473, 0.5600), (1.7664, 0.4497), (1.9891, 0.3556).

`0x402`: (−0.4571, 0.4711), (−0.2332, 0.4446), (−0.2317, 0.4178), (−0.0062, 0.4178), (0.2683, 0.4178), (0.2683, 0.4711), (0.5429, 0.4711), (0.7683, 0.4711), (0.7683, 0.4178), (0.9938, 0.4178), (1.2683, 0.4178), (1.2683, 0.4711), (1.5429, 0.4711), (1.7683, 0.4711), (1.7699, 0.4443), (1.9938, 0.4178).

`0x404` is absent. `0x403` is the short curve that lives in 0–1. Seven points. The first has Y 0.4756 and no X. Do not invent X = 0. Then (0.2499, 0.4911), (0.2523, 0.4761), (0.5008, 0.5067), (0.7485, 0.5372), (0.7564, 0.5635), (1.0000, 0.6178). If X is input and Y is output, this lifts the shadow and the low midtone and rolls the highlight off to about 0.62. The near-duplicate X values look like handles beside knots. That reading is conditional on X and Y meaning input and output. Because this node runs before the wheels, a rolloff here is then opened back up by the gain and the offset.

`0x405` is seven points. First Y is 0.4800 with no X. Then (0.2441, 0.4822), (0.2686, 0.4801), (0.4883, 0.4844), (0.7441, 0.4896), (0.7443, 0.4922), (1.0000, 0.5000). Nearly flat, ending at 0.50. A small tilt. Not a contrast curve.

`0x506`, `0x507`, `0x508`, and `0x509` are four copies of one hue curve, on the −1024..1023 scale. That scale is why they are called hue curves. The menu name of each of the four slots is **UNKNOWN**. Each copy stores the seven knots below, plus one blank point (no X, no Y) after the first knot. Do not fill the blank in.

Shared shape, 15 clips including the overhead:

| X | −1024 | 173.752 | 328.538 | 521.256 | 671.502 | 855.752 | 1023 |
|---|---|---|---|---|---|---|---|
| Y | −1024 | 209.961 | 437.183 | 629.882 | 780.113 | 891.961 | 1023 |
| Y − X | 0 | +36.2 | +108.6 | +108.6 | +108.6 | +36.2 | 0 |

A symmetric bow above the diagonal. The peak, about 109 on a 2048-wide scale, is about 5.3% of that scale. He copied one shape into all four slots.

The street (record 464) keeps Look B wheels, the shared vignette, and the shared `0x400`–`0x405` curves. Its four hue slots use a smaller bow the other way, with the peaks shifted right:

| X | −1024 | 182.114 | 368.131 | 546.343 | 709.223 | 864.114 | 1023 |
|---|---|---|---|---|---|---|---|
| Y | −1024 | 174.223 | 344.456 | 522.668 | 685.547 | 856.223 | 1023 |
| Y − X | 0 | −7.9 | −23.7 | −23.7 | −23.7 | −7.9 | 0 |

Same shape, opposite direction, about a quarter of the amplitude. The street's Glow node is in the graph and has no saved floats, so it is the plugin default. Do not paste the shared glow numbers onto that shot, and do not invent the factory defaults.

The overhead's curve payloads, including hue, match the stir byte for byte, and its wheels are Look A. Node 1 on that clip has eight children in field 9. The stir has two. Child 0 is the curves tool. Children 1–6, in tool slots 2, 3, 4, 5, 6, and 18, each store one 3×3 matrix of `1,0,0, 0,1,0, 0,0,1`. Identity. They add no color. The parameter ids are 2284847153, 2289041438, 2286944272, 2293235730, 2295332884, and 2297430029. An RGB mixer, or a similar matrix tool, that was opened and left at default will write the identity, while a tool he never opened is omitted. The overhead's real difference from the other Look A shots is the zoom-out keyframe. Child 7 is 5 bytes and did not parse. Leave it unnamed.

Copying one hue shape into all four slots is his habit, and it is not a Resolve default. Read-only, on other projects: Zaidi 6 uses the same four ids, a different bow, peak about +127. Zaidi paper graded bows the other way, down to about −112. CT's four copies are smaller, about −17 to +58. The 16-point sets at `0x400`–`0x402` also differ per project, so he edits them. Inside RH-4 they are one copied setup, not sixteen unique curves.

### Glow and vignette

Vignette is identical on all 16 camera clips. Only the keys he changed are stored:

- `controlMode` = `VIGNETTE_PLUGIN_MODE_ADVANCED`
- `resolvefxVersion` = `1.2`
- `anamorphism` = `1.2613953351974487`
- `size` = `0.694767415523529`
- `transparency` = `0.6511628031730652`

Softness is not in the saved keys. Color is not in the saved keys. An earlier color-page screenshot that read size 0.50, anamorphism 1.78, and softness 0.50 is stale. Do not copy those numbers. `transparency` is the stored name. What that slider does in the UI, beyond the name, was not checked. Do not turn 0.651 into a claim about how many stops darker the corners are.

Anamorphism above 1 stretches the vignette along the horizontal axis of the filter. On a vertical frame that puts more of the falloff on the top and bottom than on the sides. That is the geometry of the number. It was not re-checked in the viewer on this pass.

Glow is identical on the 15 clips that saved it:

- `resolvefxVersion` = `1.8`
- `brightness` = `0.1860465109348297`
- `saturation` = `0.6279069781303406`
- `spread` = `0.38759690523147583`
- `threshold` = `0.8527131676673889`
- `inputAlphaUsage` = `InputAlphaMasksSources`

Threshold near 0.85 means the glow is on the highlights. Brightness is modest. Saturation near 0.63 keeps some of the highlight color. Spread near 0.39 is a medium halo. This is not a full-frame bloom. The street has the node and none of these numbers.

The same two plugins show up on his other graded projects, with different numbers. Zaidi 6 vignette anamorphism is about 0.95 or 1.03, size 1.0, softness 0.349 on some clips, glow brightness about 0.341, threshold about 0.690. CT glow brightness is about 0.171, threshold about 0.891, spread about 0.155, vignette size about 0.898, anamorphism about 1.92. Zaidi paper graded uses size 1.0, anamorphism about 0.95, and a glow with saturation 0.558, threshold 0.698, spread 0.496. The habit is Advanced vignette plus a highlight glow, with the numbers chosen per job. **NOT A RULE** until he says the RH-4 numbers are the ones to reuse.

### What to copy, and what stays out of the style files

Copy, until he says otherwise: the section 11 story, with the sip now split. The wide shot is the end of the drink and plays first (visible source 1856–1888, full frame). The tight shot is the cup at her mouth and plays later (visible source 1786–1848, zoom 1.29, tilt −247 px). Zoom moves on this reel are slow ramps across the whole shot: a push on the opener, a widen on the lid, the overhead, the reading, the street, the bench, and the end card. A few pans and tilts are real reframes in pixels. The grade is curves, then wheels, then Glow, then Vignette, pasted per clip. Two wheel presets, split by setup (hands and table, then faces and exteriors). One hue bow copied into four slots, except the street, which uses a smaller bow the other way and leaves the glow at the plugin default. No cube.

Leave out of `references/` until he confirms any of it:

- These wheel numbers, this hue bow, this glow, and this vignette as the house grade.
- "Ignore beats," "never repeat a frame," "repeats are fine," "always end on the logo," "always punch in to 1.29" or "to 1.02."
- The end-card zoom above 1, and the end-card read past frame 67.
- The phantom keyframe pair at source 2512 and 2607.
- The rejected Foley cues.

The jobs file was overwritten to this cut after this section was written, so the next diff starts here.
