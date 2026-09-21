# CANARY NATIVE CORE

## OVERVIEW

`src/` is the C++20/C++23 server core. `main.cpp` injects `CanaryServer`; `canary_server.cpp` coordinates configuration, database, Lua/data loading, maps, game state, and service startup.

## WHERE TO LOOK

| Concern | Location |
|---|---|
| Process entry and DI | `main.cpp`, `lib/di/` |
| Startup/shutdown | `canary_server.cpp`, `server/signals.cpp` |
| Game lifecycle and scheduling | `game/` |
| Network/protocol services | `server/network/`, `server/server.cpp` |
| World/entities | `creatures/`, `items/`, `map/` |
| Persistence and migrations | `database/`, `io/`, `kv/` |
| Lua bindings | `lua/`; read `docs/systems/lua-shared-userdata.md` first |
| Crypto/transport | `security/`, `server/network/protocol/` |

## CONVENTIONS

- Each major subsystem has a local `CMakeLists.txt`; new or removed C++ files must be added to the nearest CMake list and `vcproj/canary.vcxproj`.
- `src/pch.hpp` is the common standard/library include source. Fallback includes belong under `#ifndef USE_PRECOMPILED_HEADERS`.
- C++ formatting is governed by `.clang-format` and CI, not by the nominal EditorConfig indent.
- New persistence belongs in the KV system; do not add new MySQL tables for feature state.

## ANTI-PATTERNS

- Do not use `Lua::setWeakMetatable` or the deprecated shared-pointer `pushUserdata` overload for shared Lua userdata.
- Do not capture `Player`/`Creature` ownership in deferred work; capture IDs and snapshots.
- Do not reorder protocol/client-hardcoded enum intervals.
- Do not switch to ad-hoc build directories when a preset cache can be repaired safely.

## COMMANDS

```text
cmake --preset windows-release
cmake --build --preset windows-release --target canary
cmake --preset linux-debug
cmake --build --preset linux-debug
ctest --preset linux-debug
```

Test-enabled release uses `windows-release-enabled-tests` and direct `canary_ut.exe` / `canary_it.exe` execution. Consult the parent `tibia-canary/AGENTS.md` for branch, Docker, and PR rules.
