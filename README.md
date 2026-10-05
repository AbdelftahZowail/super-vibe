# Super Vibe

One method for big work, in two modes: **Build** and **Ship**.

Super Vibe is an [OpenCode](https://opencode.ai) skill that packages two
disciplines into a single loadable context. **Build** orchestrates large or
multi-workstream work: it picks the right scale (inline vs subagents vs parallel
sessions), writes a file-ownership map so parallel writers never collide, briefs
each worker with a self-contained task, keeps the orchestrator's context lean,
then integrates the results and runs the final gates. **Ship** prepares and
performs a push/publish/release for any repo: recon against the actual repo
(never memory), scope exactly what ships, run independent adversarial
verification in fresh contexts, run the repo's own gates, commit by workstream,
then tag/push/publish and verify it landed. The method is tool-agnostic — if your
harness has no parallel-session tool, it degrades to subagents; keep the method,
bring your own tooling.

## Install

Copy the pieces into your OpenCode config:

```
skills/super-vibe/SKILL.md   →  ~/.config/opencode/skills/super-vibe/SKILL.md
commands/vibe.md             →  ~/.config/opencode/commands/vibe.md
commands/vibe-push.md        →  ~/.config/opencode/commands/vibe-push.md
```

Then use the slash commands:

- `/vibe <task>` — orchestrate a build.
- `/vibe-push <what to ship>` — prepare and perform a release.

## Part of a small ecosystem

| Repo | What it is |
| --- | --- |
| **super-vibe** (this) | The *method* for big work — Build and Ship. Portable; bring your own tooling. |
| [**brother-agent**](https://github.com/AbdelftahZowail/brother-agent) | A reference parallel-worker for OpenCode v2 (the tool the Build mode refers to). |
| [**opencode-webui**](https://github.com/AbdelftahZowail/opencode-webui) | A web frontend for the OpenCode v2 engine. |

They compose but stand alone.

## License

MIT — see [LICENSE](LICENSE).
