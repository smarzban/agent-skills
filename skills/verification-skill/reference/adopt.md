# Adopt an existing verification skill

Use this when the repo already has a verification skill in a harness-specific folder (`.cursor/skills/verify-<app>/`, `.claude/skills/verify-<app>/`), or one whose feature files lack a `Source` section. Keep its recipes and its helpers. They were proven against this app; do not regenerate them, and do not rewrite a working helper to match `generated-skill.md`. Adopt adds what is listed in step 5 and nothing else.

1. Read the whole skill: `SKILL.md`, every feature file, every helper. Run its doctor and one recipe as written, so that you know what worked before you changed anything. If it no longer works, say so and go on; the maintain pass will deal with it.
2. If the skill is not in `.agents/skills/`, move it with git, so that history follows:

   ```sh
   mkdir -p .agents/skills
   git mv .cursor/skills/verify-<app> .agents/skills/verify-<app>
   ```

   If the old folder is not tracked, move it with `mv` and say so. Then replace the old folder path in every file of the skill, helpers included, and search for it afterwards; nothing may remain.
3. Move evidence out of the skill folder and out of the home folder: change the helpers' default to `.verify/<run-id>/` at the repo root, and keep any override the helper already has. Run state inside the skill folder (a run file, a virtual environment, a `.gitignore`) moves out too; scratch that holds a secret stays in a temporary folder outside the repo. Old evidence is the owner's to keep or delete: list it and leave it.
4. Read the source for `Source` and `Automated coverage` the way `maintain.md` step 3 does: one read-only subagent per feature file, each returning the source paths the feature depends on and, for each sub-feature, the test that exercises it. Do not fill `Automated coverage` from test names.
5. Add what is missing, following `generated-skill.md` and `feature-map.md`:
   - `scripts/features-for`, beside whatever helper folder the skill already has;
   - the **Which features to drive** and **Keeping the map current** sections, the line in the opening that says what the skill does not touch, and the last clause of the frontmatter description;
   - `Source` and `Automated coverage` in every feature file;
   - the **Automated** column, the **Not mapped yet** list and **Paths with no feature** in the index. For **Not mapped yet**, list the user-facing features you can find from the routes, the commands and the docs. Run `features-for` with every tracked file as an argument (`git ls-files | xargs <verify>/scripts/features-for`) and place what it prints as `UNMAPPED`.
6. Reword an instruction that names one harness's tool so that any harness can follow it. Note in the report any instruction that depends on a file an outside contributor will not have, such as an ignored local notes file.
7. Place it, following `placement.md`. Where the old folder was `.cursor/skills/verify-<app>`, the link goes back in its place.
8. Prove it: run launch, doctor, the same recipe you ran in step 1, and cleanup, and check that the evidence is under `.verify/`. Run `<verify>/scripts/features-for --all`; it must exit 0. Driving every feature is the maintain pass's job, and adopt does not repeat it.
9. Report what moved, what was added, anything in the old skill that you changed in meaning, and that a maintain pass is the next step.
