---
description: "Super Vibe — Ship: prepare, independently verify, and perform a push/publish/release for any repo"
argument-hint: "[what to push: branch, 'release', or a description]"
---

<objective>
Prepare and perform the push/publish/release below using the Super Vibe doctrine's
**Ship** mode: recon against the repo, scope exactly what ships, run independent
adversarial verification in fresh contexts, fix and re-verify, run the repo's
gates, commit by workstream, then tag/push/publish and verify it landed.
</objective>

<execution_context>
Load the `super-vibe` skill (skill tool, id `super-vibe`) and follow its **Ship**
section end-to-end. If the skill tool is unavailable, read
`~/.config/opencode/skills/super-vibe/SKILL.md` directly and follow it.
</execution_context>

<context>
Arguments: $ARGUMENTS
</context>

<process>
1. Recon: repo docs, git log + latest tag, package/CI config, release helper doc
   — verify against the repo, never memory.
2. Scope exactly what ships and what must NOT be swept in; stage explicit paths.
3. Independent adversarial verification in fresh contexts (secrets / security /
   quality+tests / docs); fix, then re-verify.
4. Run the repo's own gates fresh — evidence or it didn't happen.
5. Commit by workstream (repo conventions); then tag/push/publish per convention
   and verify the publication landed; report with evidence and links.
</process>
