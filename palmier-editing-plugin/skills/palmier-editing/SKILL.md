---
name: palmier-editing
description: Drive the Palmier Pro video editor over its MCP to assemble and edit a timeline — cut/trim/layer clips, add captions and text, bring in generated shots, and prep exports. Use when editing video in Palmier Pro, building a YouTube video or short/Reel from footage, turning a long master into a clip, doing a caption pass, layering B-roll, or assembling AI-generated shots into a real timeline. Composes with storyboard-video-maker, seedance-prompt-builder, character-reference-sheet, and watch. Triggers on "edit my video", "cut this", "make a short", "assemble the timeline", "caption pass", "build the YouTube video", "put these clips together".
---

# Palmier editing — personal playbook

This is the *personal layer* on top of Palmier Pro's own MCP instructions (the server already
covers frame math, "call get_timeline first", the cost-gate, and model picks). Don't restate those —
this file is my defaults, my pipeline, and my reusable recipes.

## Preconditions (check first)
- Palmier Pro must be **running with a project open**. If any tool returns `Editor not available`,
  stop and tell me to open/create a project — don't retry blindly.
- Run from `~/Documents/Youtube Videos/` or `~/Documents/Palmier Pro/` (where the MCP is scoped).
- `get_timeline` returns `canGenerate`. If false, generation/upscale will fail — tell me to sign in
  + subscribe before proposing any paid step. On-device `inspect_media`/transcription still works.

## My defaults
- **YouTube long-form:** 16:9, 1920×1080, 30fps. **Shorts/Reels/TikTok:** 9:16, 1080×1920.
- Timing is in FRAMES (`frame = seconds × fps`). State edits, not the play-by-play. Keep it terse.
- Naming: prefix generated assets by role ("hero-", "broll-", "vo-") and group with folders.

## Cost discipline (hard rule)
- `generate_video` / `generate_image` / `generate_audio` / `upscale_media` cost **real money and are
  not undoable**. Always propose prompt + model + duration + aspect ratio + rough cost, then wait for
  my explicit yes. Never fire a paid tool to "just try". Clip edits are free and undoable — just do them.

## Generative pipeline (my skill suite → Palmier timeline)
Prefer my existing skills to *design* shots, then Palmier to *assemble* them:
1. **Identity lock** — `character-reference-sheet` for any recurring person/character; reuse as
   `referenceMediaRefs` / `startFrameMediaRef` so faces stay consistent.
2. **Shot design** — `seedance-prompt-builder` (single shot) or `storyboard-video-maker` (multi-shot
   sequence) to produce prompts/frames. Stills-before-video: approve an image, then animate it via
   `generate_video` with that still as `startFrameMediaRef`.
3. **Bring it in** — `import_media` (url / path / bytes) for anything made outside Palmier (Higgsfield
   outputs, stock, `~/Documents/Templates`). Then `add_clips` onto the timeline.
4. **Analyze references** — use the `watch` skill to digest a reference video before matching its style.

## Reusable recipes
**Short from a long master**
1. `get_timeline` (fps/tracks) + `get_media`. 2. `get_transcript` or `search_media` to find the hook /
the exact line. 3. `split_clip` + `ripple_delete_ranges` to keep only the strong section. 4. Reframe to
9:16 (set clip `transform`/crop via `set_clip_properties`). 5. `add_captions` from the transcript.
6. Optional punch-ins via `set_keyframes` on scale. 7. Tell me to export 9:16.

**Caption pass**
`get_transcript` → `add_captions` with a consistent style; page long timelines with startFrame/endFrame.

**B-roll layering**
`import_media` → `add_clips` on an upper video track → set opacity/transform; trim to the VO beat.

**Beat-synced cuts**
Read audio transcript/segments for timing → `split_clip` at beat frames → `move_clips` to align.

## Guardrails
- Don't re-read `get_timeline` between my own edits — mutation tools return what changed.
- Call `inspect_media` and describe what's actually on screen before editing off any asset — never the filename.
- Never ask a video model for UI screenshots, logos, title cards, or readable text — bake those with
  `add_texts` or an imported still.

## Export (manual — there is NO MCP export tool)
The agent gets the timeline finished; I render in the app (File → Export).
- YouTube: 16:9 1080p (or 4K master), H.264/HEVC.
- Shorts/Reels/TikTok: 9:16 1080×1920, ≤60s, H.264.
