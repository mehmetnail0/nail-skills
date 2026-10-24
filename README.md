# Nail skills

Five Claude Code skills: `mukemmel`, `clouding`, `fullcapacity`, `eminmisin`, `sorsor`.

## Install

1. Copy the five folders into `~/.claude/skills/` (all projects) or `<project>/.claude/skills/` (one project).
   Each folder must contain a `SKILL.md`:

   ```
   ~/.claude/skills/mukemmel/SKILL.md
   ~/.claude/skills/clouding/SKILL.md
   ...
   ```

2. Restart Claude Code (or `/clear`). Skills are read at session start.
3. Check: type `/` and the skill names appear in the list.

## How they run

- **By hand:** type `/mukemmel`, `/clouding`, `/fullcapacity`, `/eminmisin`, `/sorsor`.
- **Automatically:** Claude reads each skill's `description` line and loads the skill when the task matches
  (for example `mukemmel` on any build or video job). Nothing else to configure.

## Use

| Skill | Use it when |
|---|---|
| `mukemmel` | Before showing any build, design or render output; sets the quality bar |
| `eminmisin` | Before a change on a live product with real users; safety checklist |
| `clouding` | Before heavy work (renders, builds, model runs); run it in the cloud, not on the laptop |
| `fullcapacity` | While GPU lanes run; checks the box is really busy |
| `sorsor` | When you want Claude to ask questions first instead of guessing |

## Adapt first

`clouding` and `fullcapacity` mention the author's own setup (`fp.py`, pod lane paths, Runpod).
Edit those lines to your own tools, or they will not work as written.
