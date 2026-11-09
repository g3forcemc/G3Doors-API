# G3 Doors API Tester

Two independent consumer projects exist: Forge 1.20.1 and NeoForge 1.21.1. Neither includes
private G3 source or copies the API. Both compile against the full platform-specific G3 JAR
in their own `libs` directory. The mod ID is `g3doors_api_test` on both platforms.

## Install

Use exactly one platform's tester:

| Minecraft / loader | Tester artifact | Other required mods |
| --- | --- | --- |
| 1.20.1 / Forge 47.4.10 | g3doors-api-test-forge-1.20.1-1.0.0.jar | API-bearing G3 Doors Forge, GeckoLib Forge 4.8.3 |
| 1.21.1 / NeoForge 21.1.221 | g3doors-api-test-neoforge-1.21.1-1.0.0.jar | API-bearing G3 Doors NeoForge, GeckoLib NeoForge 4.6.6 |

Place the matching files in `mods`. G3 Doors is a mandatory metadata dependency. An older
API-less G3 JAR is insufficient even when its mod version is also 1.0.0. No API-only JAR is
required. These tester artifacts are local deliverables; this document does not claim a
public tester download or publish either tester.

## Item controls

Creative tab and item name: **G3 Doors API Tester**. The item uses the vanilla blaze rod icon.
It can also be obtained with:

```text
/give @s g3doors_api_test:tester
```

Hold it in the main hand and use a supported door or existing proxy:

- Right-click: `G3DoorsApi.requestToggle()`; shows the returned operation result.
- Shift + right-click: `G3DoorsApi.getSnapshot()`; shows lookup status, registry ID,
  resolved main position, phase, movement, restrictions, optional stored target and MTD group size/limit.

The event handler consumes the interaction, so native block use cannot execute an additional
toggle or bypass a rejected API request. Failure result names are displayed unchanged.
Examples: `BUSY`, `CONTROLLER_LOCKED`, `OBSTRUCTED`, `UNLOADED`, `UNSUPPORTED`.
`ACCEPTED` reports acceptance, not animation completion.

## Operator probes

Requires permission level 2. These commands use the same public API and do not force-load targets:

```text
/g3api_test <x> <y> <z> snapshot
/g3api_test <x> <y> <z> toggle
/g3api_test <x> <y> <z> open
/g3api_test <x> <y> <z> close
```

Command probes also log each result with its position and action, including when invoked
by a vanilla test function. They do not change API restrictions. A remote unloaded target
can be probed using `/g3api_test 29000000 64 29000000 snapshot`.

## Automated checks

`build` runs output-format regression checks and scans the consumer bytecode for internal
G3 references, copied API classes, Java sources and embedded dependency JARs. It also compiles
the complete Java example and legacy command-only fixture against the normal binary dependency.
These checks do not simulate a running world; consult [TESTING.md](TESTING.md) for live evidence.

## Manual coverage checklist

1. Conventional door: inspect both halves; toggle, explicitly open twice, close.
2. Animated door: observe settled and moving snapshots; repeat a command during motion.
3. Hatch and Ramp: request the opposite target while moving; verify `QUEUED`, stored target and final pose.
4. MTD: join adjacent modules; inspect group size; use one module and inspect another.
5. Existing proxy: inspect and control it; verify resolution to the main position.
6. Controller: link using the native controller item; verify API requests are restricted,
   including another member of the same MTD group. Unlink through normal gameplay.
7. Obstruction: open an MTD, place a solid block in its closing footprint; expect `OBSTRUCTED`.
8. Unsupported block and unloaded coordinates: expect normal failure results, no forced load.

The automated test functions used in the local worlds are test fixtures, not part of the
public API. They never invoke internal G3 classes. Not every mounting direction, door family,
modpack or chunk-unload timing is certified by these representative checks.
