# CANARY SHARED DATA

## OVERVIEW

`data/` is the shared Lua/XML runtime layer loaded by the native server before datapack-specific content. It contains `core.lua`, `global.lua`, libraries, XML definitions, event registrations, and reusable module scripts.

## WHERE TO LOOK

| Concern | Location |
|---|---|
| Bootstrap order | `core.lua`, `global.lua`, `events/`, `modules/` |
| Shared Lua APIs | `libs/`, `lib/core/`, `libs/functions/` |
| XML game definitions | `XML/`, `items/`, `chatchannels/` |
| Reusable event scripts | `events/scripts/`, `scripts/` |
| JSON schedulers | `json/eventscheduler/` |
| Datapack-specific content | sibling `data-canary/` and `data-otservbr-global/` |

## CONVENTIONS

- C++ startup controls load order; a script can depend on earlier core libraries and XML registrations being present.
- Keep shared helpers generic. Put world/monster/NPC-specific behavior in the appropriate datapack sibling.
- Lua formatting and lint behavior follows the repository `.editorconfig`, StyLua, and CI configuration.
- New persistent feature state uses the native KV interface exposed to Lua; do not introduce a new SQL table from this layer.

## ANTI-PATTERNS

- Do not assume a Lua file is standalone; trace its module/event loader and datapack consumers.
- Do not duplicate global helpers in a datapack when the behavior belongs in shared `data/libs`.
- Do not change protocol-facing enum/order definitions from Lua without checking the native/client contract.

## VALIDATION

Run these commands from the `tibia-canary/` repository root, not from this directory:

```text
for f in tests/lua/test_*.lua; do luajit "$f"; done
python -m tools.canary_audit validate-schemas
```

Native build and database-backed integration test commands are documented in the parent repository policies and `tests/AGENTS.md`.
