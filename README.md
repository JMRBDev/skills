# JMRBDev Skills

Reusable agent skills published through the [Skills CLI](https://skills.sh/).

## Available skills

### ship-feature

Ships substantial software features through a controlled loop:

> discover → plan → implement → prove → review → repair

It coordinates dedicated planning, implementation, and independent review subagents while the main agent owns repository safety, decisions, evidence, Git history, and PR state.

[Read the skill](skills/ship-feature/SKILL.md) · [Role contracts](skills/ship-feature/ROLE-CONTRACTS.md) · [skills.sh](https://skills.sh/jmrbdev/skills/ship-feature)

## Install

Install one skill globally:

```bash
npx skills add JMRBDev/skills --skill ship-feature -g -y
```

Install one skill into the current project:

```bash
npx skills add JMRBDev/skills --skill ship-feature -y
```

List the skills in this repository:

```bash
npx skills add JMRBDev/skills --list
```

Install every skill:

```bash
npx skills add JMRBDev/skills --all -g
```

Update globally installed skills:

```bash
npx skills update -g
```

## Repository layout

Each installable skill has its own directory under `skills/`:

```text
skills/
└── <skill-name>/
    ├── SKILL.md
    └── optional companion files
```

The repository root intentionally has no `SKILL.md`, allowing the CLI to discover every skill without `--full-depth`.

## Adding a skill

1. Create `skills/<skill-name>/SKILL.md`.
2. Keep references used by the skill inside the same directory.
3. Give the skill a unique kebab-case `name` in its YAML frontmatter.
4. Verify discovery:

   ```bash
   npx skills add . --list
   ```

5. Test a clean copied installation before release.
6. Add the skill to the Available skills section and [`CHANGELOG.md`](CHANGELOG.md).

## License

[MIT](LICENSE)
