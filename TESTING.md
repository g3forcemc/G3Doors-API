# Verification report

Verified on **2026-11-09**. Automated checks, live command probes and physical item
interactions are distinguished below. These are representative integration tests, not
certification of every door/orientation/modpack.

## Binary dependencies tested

API contract version: 1.0.0. Both normal full G3 mod JARs still have local mod version 1.0.0.
These are rebuilt API-bearing binaries, not a claim that older public 1.0.0 files contain the API.

| Platform | Minecraft / loader | Java | G3 dependency SHA256 |
| --- | --- | --- | --- |
| Forge | 1.20.1 / 47.4.10 | 17 | `66dfcfc46c966fbf32e709cc43144e0e6eade87a308540fa7b9c93c572086f62` |
| NeoForge | 1.21.1 / 21.1.221 | 21 | `bfe202e99993d360c6527545c8eaa31eab949c5108004620b235f5a94eee8069` |

GeckoLib: Forge 4.8.3; NeoForge 4.6.6. Forge's development client also loaded the optional
local MOB test mod; NeoForge did not load that Forge-only dependency. This does not certify
compatibility with arbitrary third-party mods.

## Automated checks

| Check | Forge | NeoForge |
| --- | --- | --- |
| Independent tester clean build | PASS | PASS |
| Public API DTO/output formatting | 11 checks PASS | 11 checks PASS |
| Consumer bytecode API-boundary scan | PASS | PASS |
| Full Java example and legacy smoke sources compiled against normal binary JAR | PASS | PASS |
| G3 API contract regression, rerun | 42 checks PASS | 42 checks PASS |
| Actual property reader: 28 animated families / four phases / conventional states | Not run as a separate test on Forge | 267 checks PASS |
| Separate API JAR or private source needed by consumer | No | No |
| Copied G3 classes, Java source or embedded dependency JAR in tester artifact | None | None |

The contract checks cover request policy, idempotence, BUSY/queued-target handling,
controller precedence, immutable snapshots and guarded unloaded reads. They do not emulate
an entire ServerLevel. NeoForge's actual-block-state regression uses the production state
reader but does not prove every family's full live animation.

Forge's consumer distribution is processed through ForgeGradle reobfJar. NeoForge uses its
normal ModDevGradle output. Documentation examples are a separate compilation source set
and are not added to the installable tester JAR.

## Live running-world checks in this verification pass

Both independent clients started successfully, one version at a time, with the full normal
G3 dependency. Native item use placed the fixtures in each project's own Creative test world.
Both clients saved all dimensions and exited before completion. No installed CurseForge world
was used for these new probes.

Door commands below ran through the consumer's public API command handler in a real integrated
server. Vanilla test functions issued successive commands in the same tick and scheduled later
snapshots to test motion reliably. Each API result was explicitly logged with position/action;
function completion alone was not counted as proof.

| Scenario | Forge | NeoForge | Observed result |
| --- | --- | --- | --- |
| Conventional Simple Door 1, upper-half resolution | PASS | PASS | FOUND; main position resolves to lower half |
| Conventional open/close and repeated open | PASS | PASS | ACCEPTED / ALREADY_IN_STATE; OPEN and CLOSED snapshots |
| Unsupported block | PASS | PASS | UNSUPPORTED |
| Remote unloaded lookup and open | PASS | PASS | UNLOADED |
| Hatch Door 1 open, queued close during opening | PASS | PASS | ACCEPTED, OPENING, QUEUED; stored target CLOSED; final CLOSED |
| Hatch stable open and close | PASS | PASS | OPEN then CLOSED after native motion |
| Ramp open, queued close during opening | PASS | PASS | ACCEPTED, OPENING, QUEUED; stored target CLOSED; final CLOSED |
| Ramp stable open and close | PASS | PASS | OPEN then CLOSED after native motion |
| Two-module MTD snapshot | PASS | PASS | Group size 2 / limit 20 |
| MTD proxy lookup and open from proxy | PASS | PASS | FOUND; correct module main position; ACCEPTED |
| MTD request on another member during opening | PASS | PASS | OPENING; close returns BUSY |
| Both MTD members after opening | PASS | PASS | OPEN |
| Solid block obstructing one member, close requested on the other | PASS | PASS | OBSTRUCTED; group remains open |
| Obstacle removed, group close | PASS | PASS | ACCEPTED; both members finish CLOSED |
| Controller linked natively to second MTD member | PASS | PASS | First member snapshot has CONTROLLER_LINKED |
| API requests on both controller-owned members | PASS | PASS | CONTROLLER_LOCKED |

Fixture coordinates: Simple Door main (40,-60,1), Hatch (48,-60,1), Ramp (64,-40,1),
MTD modules (80,-60,1) and (81,-60,1), controller (83,-60,1), MTD obstacle (80,-57,1).
The existing MTD proxy at (80,-59,1) resolved to (80,-60,1).
A ground-level Forge ramp at (64,-60,1) first returned OBSTRUCTED because of terrain;
the elevated fixture provided native clearance for movement tests. This was fixture setup,
not an API repair or bypass.

## Actual tester-item interactions

Earlier live sessions already verified the Forge item's MTD inspect/toggle, group operation,
controller rejection, obstruction rejection and Ark BUSY; NeoForge's repaired MTD item cycle
was also verified. Those historical sessions used preceding G3 builds and are not presented
as a fresh item retest of every case on the hashes above.

In this pass NeoForge's actual Tester item additionally:
- Opened Simple Door 1 with normal native Use Item: ACCEPTED and visible opening.
- Queried the closed door with Shift + Use Item: FOUND, correct registry ID/main position,
  CLOSED, Moving=false, Restrictions=none.

The isolated development profiles map native Use Item to U and use toggle crouch for automation.
The actual RightClickBlock event and Shift state run normally. Installed tester JARs do not
change the player's key bindings.

## Fixed issue retained in the tested NeoForge binary

G3-API-NEO-001 previously caused a property-identity exception on animated doors such as MTD.
It was fixed in an earlier authorized task; the 267-check state-reader regression and the live
MTD checks now pass. No G3 API or implementation repair was made during this documentation/
consumer verification pass.

## Remaining manual checks and limits

- Every one of the 28 animated families, every conventional variant and every mounting direction.
- Hatch 2/3/4 live queued-target variations; the live representative here was Hatch 1.
- Lab paired-controller cases, automatic closing, all native obstruction families.
- MTD 20/21 boundary, staggered/horizontal groups, split/rejoin and entity push/abort through API.
- Chunk unload/reload during a started animation, boundary chunks while another is loaded,
  interrupted-animation recovery and removal of open-passage proxy targets.
- Dedicated-server multiplayer, client/server version mismatch and arbitrary modpack combinations.
- Public Maven resolution: there is no G3 Maven publication to test.
- Installing a future uniquely versioned public G3 release; this pass used the listed local JARs.

No new reproducible API failure was found in the listed checks. Native delayed animation and
later chunk-unload behavior were not rewritten. API resolution/entry guards do not promise
a redesigned asynchronous lifecycle.

## Reproduce

In either independent tester project run `gradlew.bat build --console=plain`.
It checks the consumer boundary, formatting and compilation of the complete documentation
example plus legacy smoke fixture against the normal G3 binary. Use `--offline` only after
dependencies are cached.

In the private G3 projects: `gradlew.bat verifyDoorApi --offline --console=plain`.
NeoForge additionally provides `verifyDoorApiState`. Follow [TESTER.md](TESTER.md) for live
item/command probes; use [the Forge](GRADLE-FORGE.md) or [NeoForge](GRADLE-NEOFORGE.md) local-JAR
guide for an integration with no private source checkout.

No task in this verification published a repository, release or Maven artifact.
