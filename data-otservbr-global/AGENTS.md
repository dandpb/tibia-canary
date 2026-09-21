# CANARY GLOBAL DATAPACK

## OVERVIEW

`data-otservbr-global/` is the large production-style Lua datapack. It is loaded after shared `data/` and supplies world, monster, NPC, raid, quest, migration, and gameplay content.

## STRUCTURE

```text
data-otservbr-global/
├── scripts/       # reusable and event-driven gameplay scripts
├── monster/       # monster definitions and behavior
├── npc/           # NPC definitions and handlers
├── world/         # quests, world changes, and map-facing content
├── raids/         # raid definitions and schedules
├── migrations/    # datapack data migrations
└── lib/           # datapack-local helpers
```

## WHERE TO LOOK

- Startup/readme notes: `startup/` and repository `data/core.lua`.
- Quest/world behavior: `world/` and its local README files.
- NPC behavior: `npc/` and `scripts/npc/`.
- Monster behavior: `monster/` and `scripts/monster/`.
- Scheduled content: `raids/`, `scripts/globalevents/`, and scheduler JSON under shared `data/`.

## CONVENTIONS

- Prefer existing shared constructors and helpers (`Action`, `Spell`, `MonsterType`, `NpcType`, `EventCallback`) over new registration mechanisms.
- Keep datapack behavior in this tree; shared engine/library changes belong in `data/` or native `src/`.
- Validate changes with the target datapack runtime smoke, LuaJIT syntax/tests, and the relevant server test filter.

## ANTI-PATTERNS

- Do not assume every tracked script is loaded; confirm its registration path and load phase.
- Do not edit generated or downloaded map/runtime artifacts as if they were source definitions.
- Do not add a new persistence table for a datapack feature; use the existing KV-facing API.
