# palmier-editing

Personal Palmier Pro video-editing playbook, packaged so it loads in **Claude Cowork and chat** (Claude Code reads the same skill from `~/.claude/skills/`).

## What it does
Bundles one skill — `palmier-editing` — that drives the Palmier Pro video editor over its MCP: cut/trim/layer clips, caption passes, bringing in AI-generated shots, and prepping exports. It carries personal defaults (YouTube 16:9/30, 9:16 shorts), a Seedance/Higgsfield → timeline pipeline, reusable recipes (short-from-master, caption pass, B-roll layering, beat-sync), and a cost-gate on paid generation.

## Requirements
- **Palmier Pro** app installed and **running with a project open** (tools return `Editor not available` otherwise).
- The **palmier-pro MCP extension** installed/enabled in the Claude app (this plugin does not redefine the MCP server — it uses the already-installed one to avoid a duplicate connection).
- Generation tools (`generate_*`, `upscale_media`) need a Palmier login + subscription (`canGenerate`).

## Triggers
"edit my video", "make a short", "caption pass", "assemble the timeline", "build the YouTube video", "put these clips together".

## Composes with
`character-reference-sheet`, `seedance-prompt-builder`, `storyboard-video-maker`, `watch`.
