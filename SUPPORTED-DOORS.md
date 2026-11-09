# Supported doors

Only G3 Doors registry entries are accepted. Vanilla and third-party DoorBlock instances
are deliberately not advertised as G3 API targets.

## Animated families (28)

- Elevator Door
- Sliding Security Door
- Plasma Door
- Ship Door
- Ark Door
- G3 Door 3x3 and G3 Door 3x3 V2
- Factory Door 1
- Security Door 1 and Security Door 2
- Security Door 3 / MTD (`g3project_doors_reborn:securitydoor3`)
- Silo Door
- Tech Door 1 and Tech Door Glass
- Lab Doors 1, 2, 3, 4, 5 and 6
- Arch Door
- Iris Door
- BigO Door
- Hatch Doors 1, 2, 3 and 4
- Ramp

Existing main blocks and recognized proxy blocks are resolved through native door managers.
Open passage cells whose proxies were removed are not targets.
Hatch/Ramp accept stored target changes during motion; other families report BUSY.
Native obstruction checks are used for Arch, Iris, BigO, Security2, MTD and Ramp.
Other families retain their own existing behavior; the API does not invent obstruction checks.

## Conventional doors

G3 HandOpenIronDoorBlock and ProtectedDoorBlock entries, plus HandOpenIronTrapDoorBlock entries.
Upper halves of conventional doors resolve to their lower main block.
Trapdoor changes reuse the existing state/sound operation through an internal bridge.

## MTD

- Group limit: 20 modules.
- Native alignment, one-block offset and mounting rules are preserved.
- Breaking a module preserves native group splitting.
- A controller attached to any member restricts the entire current group.
- Snapshot group data is read without persisting or changing legacy group IDs.
- Group membership is a point-in-time observation, not a permanent external identifier.
- Existing proxies in the folded base remain valid targets. Empty open-passage cells do not.

This list describes adapter support, not a claim that every family, placement orientation and
interaction has been exercised in a live world. See the per-platform evidence in [TESTING.md](TESTING.md).

## Not included in API 1

- Capability bridges.
- Registering third-party machines as persistent G3 controllers.
- A persistent lock/unlock or ownership-management API.
- Arbitrary collision, animation or texture mutation.
- Animation completion events or cancellable control events.
- Client-side world mutation.
- Universal cross-Minecraft binary compatibility.

## Main-block registry IDs

All entries below use the `g3project_doors_reborn` namespace.

- `ramp`
- `hatch_door3`
- `hatch_door2`
- `hatch_door4`
- `hatch_door1`
- `sliding_security_door`
- `g3_door_3x3`
- `g3_door_3x3_v2`
- `factory_door1`
- `security_door1`
- `tech_door1`
- `tech_door_glass`
- `silo_door`
- `plasma_door`
- `ship_door`
- `security_door2`
- `securitydoor3`
- `iris`
- `arch_door`
- `bigo`
- `ark`
- `elevator_door`
- `lab_door1`
- `lab_door2`
- `lab_door3`
- `lab_door4`
- `lab_door5`
- `lab_door6`
- `iron_door_1`
- `iron_door_2`
- `iron_door_3`
- `iron_door_4`
- `iron_trapdoor_1`
- `iron_trapdoor_2`
- `simple_door_1`
- `simple_door_2`
- `simple_door_3`
- `simple_door_4`
- `simple_door_5`
