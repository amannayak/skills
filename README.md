# Skills

A collection repository for Pi/agent  public skills.


## Repository layout

```text
.
├── README.md
├── LICENSE
└── skills/
    └── expo-mobile-ui/
        ├── SKILL.md
        └── reference/
            ├── init.md
            ├── craft.md
            ├── shape.md
            └── ...
```

## Included skills

### `expo-mobile-ui`

A skill for designing, auditing, polishing, and implementing production-grade mobile UI/UX in Expo and React Native apps.

Path:

```text
skills/expo-mobile-ui/SKILL.md
```

Invoke after installation with:

```text
/expo-mobile-ui [command] [target]
```

Examples:

```text
/expo-mobile-ui init
/expo-mobile-ui craft onboarding flow
/expo-mobile-ui polish app/(tabs)/index.tsx
/expo-mobile-ui safe-zone checkout screen
/expo-mobile-ui performance transaction list
```

## Adding a new skill

Create a new folder under `skills/`:

```text
skills/<skill-name>/
├── SKILL.md
└── reference/          # optional supporting docs
```

Recommended rules:

1. Keep one skill per folder.
2. Put the skill manifest at `skills/<skill-name>/SKILL.md`.
3. Put supporting instructions, examples, and command docs inside that same folder.
4. Keep relative links inside the skill folder, for example `reference/init.md`.
5. Keep the YAML frontmatter `description` short.

## Skill metadata rule

Pi validates skill metadata from the YAML frontmatter at the top of each `SKILL.md`.

The `description` field must be **1024 characters or fewer**. If it is longer, Pi shows an error like:

```text
description exceeds 1024 characters (1137)
```

Use a concise frontmatter description and move detailed behavior into the markdown body of `SKILL.md` or files under the skill's `reference/` directory.
