# Canary-specific guidance

The global Git, commit, PR, C++ header, exception, and documentation policies apply. This file only records Canary-specific gates.

## Recurring Defect Prevention

- For a reusable defect, inspect analogous paths by behavior and ownership, fix confirmed siblings atomically, and keep the audit proportional; do not turn a one-off into a speculative refactor.
- Decide whether tooling makes recurrence impossible. If not, add a narrow rule to the nearest `AGENTS.md` that states the unsafe pattern, required alternative, and validation rather than incident history.
- Prefer enforceable safeguards—types, helpers, static checks, architecture docs, or focused tests—especially for lifetime, arithmetic, identity, ownership, bounds, and cancellation escapes.

## Deferred Callback Lifetime Safety

- Assume scheduled, deferred, timer, and worker callbacks can outlive their source object or state.
- Never capture raw `this`, references, iterators, or mutable-container pointers across that boundary. Use immutable values plus `std::weak_ptr` or re-resolvable identity validated with the original identity, generation, epoch, or session token.
- Removal, replacement, reload, or reinterpretation must cancel pending work or advance a checked generation. Callback-owning types are non-movable unless moving cancels or safely rebinds every event.
- Use bounded arithmetic for intervals; stale work must become a no-op before gameplay, Lua, combat, movement, persistence, or client output. Cover destroyed/replaced owners, reused IDs, shutdown, and ownership transfer where practical.

## Static Ownership Lifetime Safety

- Never rely on cross-translation-unit static destruction order for caches, registries, or other global owners of gameplay objects. Prefer runtime-owned state; when global ownership is unavoidable, provide an explicit idempotent drain during controlled shutdown.
- Stop and join every producer and consumer before draining global ownership, and release retained objects while all services their destructors may access are still alive. Validate shutdown with retained entries and lifetime instrumentation where practical.

## Canary build discipline

- Before an authorized local build, read `docs/building/local-validation.md`; its maintained entry-point, environment, preset, cache, and MSVC Ninja workflow is mandatory.
- C++ source/header additions, removals, and renames must update every maintained entry: the relevant CMake list, server `vcproj/canary.vcxproj`, and test CMake list when applicable.

### MSVC Ninja dependency tracking

- Before configuring, repairing, or auditing an MSVC Ninja build, read `docs/building/local-validation.md#msvc-ninja-dependency-tracking`. Its code-page, launcher, dependency-log, and concurrency rules remain mandatory.

## Precompiled Header Policy

- `src/pch.hpp` owns broad shared standard includes; do not duplicate an unguarded PCH include.
- Headers must declare their public dependencies. When a source needs a PCH-provided include without PCH, guard it with `#ifndef USE_PRECOMPILED_HEADERS`; add broad includes to the PCH with the same local fallback.

## Lua Shared Userdata Gate

- Before changing `std::shared_ptr` Lua userdata, read `docs/systems/lua-shared-userdata.md` and use its typed trait, registration, and push helpers.
- Never combine shared `pushUserdata` with a manual metatable, use a weak metatable for shared userdata, or wrap a borrowed object without a no-op deleter. Run the document's two `rg` checks and investigate every match.

## Docker Quickstart Policy

- The Docker quickstart is intended for non-expert users to run a local Canary stack with minimal setup.
- Keep CI/build Docker, local development Docker, and user-facing quickstart Docker as separate responsibilities unless a change explicitly documents why they must overlap.
- `docker/docker-compose.yml` must keep `login-server` as the default client login webservice.
- Do not point clients to MyAAC `login.php`.
- The MyAAC quickstart image must not include or expose `login.php`; MyAAC is used only as the website/admin AAC.
- The default client login URL is `http://localhost:8088/login`.
- The default web/admin URL is `http://localhost:8080`.
- MyAAC must build from the `slawkens/myaac` `develop` branch unless a compatibility reason is documented.
- Public Docker env vars for Canary should use the `CANARY_*` prefix. Avoid adding new public `MYSQL_*`, `OT_*`, or raw Lua config variable names.
- The quickstart must not require compiling Canary locally; use the published Canary runtime image.

## PR Communication Policy

- Do not post any PR comments/reviews automatically.
- Only post PR comments/reviews when the user explicitly asks.
- All PR comments/reviews posted by me must be in English.

## Repository Map

- `src/` is the native server core; see `src/AGENTS.md` for subsystem and CMake boundaries.
- `data/` is the shared Lua/XML runtime layer; see `data/AGENTS.md`.
- `data-otservbr-global/` is the large global datapack; see its local `AGENTS.md` before changing world content.
- `tests/` contains native unit/integration suites; see `tests/AGENTS.md` for discovery, database, and stale-script caveats.
- Root startup is `src/main.cpp`; normal local builds use CMake presets, not a root-level Makefile.
- For quickstart changes, read `docs/docker/quickstart-for-beginners.md` and `docker/DOCKER.md`; keep CI/build, development, and user quickstart responsibilities separate.
- The default client path is `login-server` at `http://localhost:8088/login`, never MyAAC `login.php`. MyAAC remains website/admin-only, uses `slawkens/myaac` `2.x`, and keeps `http://localhost:8080`; public config stays `CANARY_*`, and the quickstart uses the published Canary runtime image.
