# CANARY TESTS

## OVERVIEW

`tests/` contains GoogleTest unit and integration executables linked to `canary_core`; `tools/canary_audit/tests` and `tests/lua/` are separate Python and LuaJIT validation surfaces.

## STRUCTURE

```text
tests/
├── unit/          # fast native tests by domain
├── integration/   # database/assets/server integration tests
├── fixture/       # shared fixtures and test resources
├── lua/           # direct LuaJIT scripts
└── CMakeLists.txt # setup_test and discovery policy
```

## CONVENTIONS

- Add new C++ test sources to the nearest `CMakeLists.txt` with `target_sources`; files on disk are not automatically part of the suite.
- Tests use `setup_test`, GoogleTest discovery, serial execution, and resource locks. Do not assume CTest parallelism makes cases concurrent.
- Integration tests use guarded test database reset variables and repository-root asset discovery; keep the injector alive through process exit.
- `tests/integration/game/batch_update_it.cpp` is currently present but not wired into CMake; do not infer coverage from filesystem presence.

## COMMANDS

```text
cmake --preset linux-debug
cmake --build --preset linux-debug
ctest --preset linux-debug

cmake --preset windows-release-enabled-tests
cmake --build --preset windows-release-enabled-tests
build/windows-release-enabled-tests/tests/unit/canary_ut --gtest_filter=Suite.Case
build/windows-release-enabled-tests/tests/integration/canary_it --gtest_filter=Suite.Case
python -m unittest discover -s tools/canary_audit/tests -t . -p "test_*.py" -v
```

## ANTI-PATTERNS

- Do not use stale `tests/build_and_run.sh` as the authoritative command source; it references obsolete options and Catch2 flags.
- Do not reset arbitrary databases. Use a test-like database name and the explicit guarded reset flag only.
