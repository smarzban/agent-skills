# Adopt an existing verification skill

Use this when the repo already has a verification skill in a harness-specific folder (`.cursor/skills/verify-<app>/`, `.claude/skills/verify-<app>/`), or one that lacks the `Source` and `Automated coverage` sections. Keep its recipes. They were proven against this app; do not regenerate them.

1. Read the whole skill: `SKILL.md`, every feature file, every helper. Run its doctor and one recipe as written, so that you know what worked before you moved anything. If it no longer works, say so and switch to the maintain pass after the move.
2. Move it with git, so that history follows:

   ```sh
   mkdir -p .agents/skills
   git mv .cursor/skills/verify-<app> .agents/skills/verify-<app>
   ```

   If the old folder is not tracked, move it with `mv` and say so.
3. Replace the old folder path in every file of the skill, helpers included, with `.agents/skills/verify-<app>`. Search for the old path afterwards; nothing may remain.
4. Move run state and evidence out of the skill folder. Change the helpers to write under `.verify/<run-id>/` at the repo root, and remove any `.gitignore`, virtual environment or run file inside the skill folder. Old evidence in the skill folder is the owner's to keep or delete: list it and leave it.
5. Add what is missing, following `generated-skill.md` and `feature-map.md`:
   - the **Which features to drive** and **Keeping the map current** sections, and `scripts/features-for`;
   - `Source` and `Automated coverage` in every feature file;
   - the **Automated** column and the **Not mapped yet** list in the index.
6. Reword an instruction that names one harness's tool so that any harness can follow it.
7. Place it, following `placement.md`. Where the old folder was `.cursor/skills/verify-<app>`, the link goes back in its place.
8. Prove it as in Create step 6, with the same recipe you ran in step 1.
9. Report what moved, what was added, and anything in the old skill that you changed in meaning.
