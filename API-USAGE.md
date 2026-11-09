# Using the API

All methods are static on `G3DoorsApi`. Call with a non-null `ServerLevel` and `BlockPos`
on the **server thread**. Null arguments throw `NullPointerException`.
An off-thread call returns `WRONG_THREAD` before reading world state.
Schedule work from asynchronous code using the Minecraft server executor; never block the server thread.

## Resolve a door and inspect it

```java
import hu.g3projectdoorsreborn.api.DoorLookup;
import hu.g3projectdoorsreborn.api.DoorLookupStatus;
import hu.g3projectdoorsreborn.api.DoorSnapshot;
import hu.g3projectdoorsreborn.api.G3DoorsApi;
import net.minecraft.core.BlockPos;

// Inside server-thread code; level is a ServerLevel and clickedPos is a BlockPos.
DoorLookup lookup = G3DoorsApi.findDoor(level, clickedPos);
if (lookup.status() == DoorLookupStatus.FOUND) {
    DoorSnapshot door = lookup.snapshot().orElseThrow();
    boolean settledOpen = door.isOpen();
    boolean moving = door.isMoving();
    BlockPos main = door.mainPos();
    door.targetOpen().ifPresent(target -> { /* stored desired target */ });
    door.group().ifPresent(group -> { /* group.members(), size(), limit() */ });
}
```

`getSnapshot(level, pos)` is an alias of `findDoor` and always re-reads the world.
Lookup statuses: `FOUND`, `UNLOADED`, `UNSUPPORTED`, `INVALID_STATE`, `WRONG_THREAD`.
Only `FOUND` contains a snapshot.

Snapshots, group members and restrictions are immutable defensive copies.
They do not retain internal block entities, managers or group UUIDs. They are not live handles.
Re-query after block replacement, a group split, chunk changes or animation progress.
`doorType()` is the block's registry ID, not its translated display name.
`mainPos()` identifies the resolved module. An MTD group has no public permanent leader.
An MTD snapshot's phase is that module's phase; query other member positions when needed.

`OPEN`/`CLOSED` mean settled phases. They are not promises about exact collision geometry,
entity passage or future auto-close. No voxel-shape/passability API is provided in version 1.

## Open, close and toggle

```java
// Import hu.g3projectdoorsreborn.api.DoorCommandResult and G3DoorsApi.
DoorCommandResult opening = G3DoorsApi.requestOpen(level, clickedPos);
DoorCommandResult closing = G3DoorsApi.requestClose(level, clickedPos);
DoorCommandResult toggled = G3DoorsApi.requestToggle(level, clickedPos);
```

These are independent examples; normally call only the operation you need.
OPEN and CLOSE are idempotent. Repeating OPEN never closes an open door.
For Hatch/Ramp, toggle reverses the stored target during motion. Other animated families
return `BUSY` during motion; the API never forces an animation to settle.
Only Hatch/Ramp accept a new queued target in API version 1.
Use `supportsQueuedTarget()` to inspect this capability rather than guessing from a display name.
Pending-close state on Silo/Tech doors is observable when present, but this does not imply
that those doors accept arbitrary queued API requests.

| Result | Meaning |
| --- | --- |
| ACCEPTED | A new transition/state change was observed after the native handler ran. |
| QUEUED | The native Hatch/Ramp handler stored a different target during motion. |
| ALREADY_IN_STATE | The stable state or already stored motion target matches. |
| BUSY | Existing motion/interaction locks prevent the request. |
| OBSTRUCTED | A native obstruction preflight rejected the transition. |
| CONTROLLER_LOCKED | A G3 controller owns the door, coupled Lab partner or MTD member. |
| UNLOADED | A required chunk is unloaded. No load was requested by the API. |
| UNSUPPORTED | Not a supported G3 door or existing proxy. |
| INVALID_STATE | A required main block/entity is missing or inconsistent. |
| WRONG_THREAD | Not the server thread. |
| REJECTED | The native handler did not produce the requested transition/target. |

`changed()` is true only for `ACCEPTED` and `QUEUED`. It does not mean animation completion.
A closing MTD can subsequently reopen if its native entity-safety logic aborts closing.
The API does not reserve a passage or override future redstone/controller activity.

## Existing behavior

Existing manual-control paths are used where available, including automatic closing and
Lab 1-3 paired operation. Silo/Tech1 use their existing targeted transition logic through
small internal bridges, without controller-toggle shortcuts or clearing locks.
Controller ownership is checked before mutation. There is no public ownership bypass,
controller registration, persistent lock setter or arbitrary state setter.
MTD commands always go through the native group manager and its 20-member limit.

## Loaded chunks

Resolver reads are guarded, including proxy pointers and fallback searches. A missing
required chunk produces `UNLOADED`, not an incomplete success or a forced load.
Before a mutating native handler runs, version 1 conservatively requires loaded chunk
columns intersecting a horizontal radius of **17 blocks around each affected main block**
for animated doors (1 block for conventional doors). This covers existing legacy proxy
repair scans and neighboring columns. It may reject a command even if the visible door
itself is fully loaded. The API does not add chunk tickets.

This entry-point guarantee does not rewrite existing native animation tickers, delayed
callbacks or chunk-reload recovery. Their behavior after a later chunk unload remains
that of the original mod and is not a new asynchronous safety guarantee.

A lookup can succeed while a command returns `UNLOADED` because commands require this
larger safety area. A required unavailable MTD neighbor prevents a complete group lookup.
Removed open-passage proxies are air and are not accepted as door targets.

## Compilable example and test item

[DoorApiExamples.java](examples/DoorApiExamples.java) is a complete compilation unit. Its imports
and all public calls are compiled against the full normal mod JAR on both platforms. The snippets
above are method-body examples and require a server-level/position supplied by the caller.

The [API Tester](TESTER.md) uses `requestToggle()` on use and `getSnapshot()` on sneak-use.
Failed requests show the API result and do not fall back to native block interaction.
