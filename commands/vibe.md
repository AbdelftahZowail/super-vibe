---
description: "Super Vibe — Build: orchestrate large or multi-workstream work (choose the scale, ownership map, self-contained briefs, integrate and verify)"
argument-hint: "[task or plan to orchestrate]"
---

<objective>
Build the task below using the Super Vibe doctrine's **Build** mode: choose the
right scale, write a file-ownership map, brief workers with self-contained tasks,
keep the orchestrator's context lean, then integrate, verify, and report.
</objective>

<execution_context>
Load the `super-vibe` skill (skill tool, id `super-vibe`) and follow its **Build**
section end-to-end. If the skill tool is unavailable, read
`~/.config/opencode/skills/super-vibe/SKILL.md` directly and follow it.
</execution_context>

<context>
Arguments: $ARGUMENTS
</context>

<process>
1. Pick the scale: trivial → inline; one coherent task → subagents only;
   multiple independent workstreams → one worker each, in parallel.
2. Plan first and get user approval before launching workers, unless the user
   already approved the approach or the task is trivial.
3. Build the file-ownership map (one active writer per file) and put it in every
   brief.
4. Launch workers; keep your own context lean; integrate the cross-file seams.
5. Run the final gates (typecheck / build / tests) and report with evidence.
</process>
