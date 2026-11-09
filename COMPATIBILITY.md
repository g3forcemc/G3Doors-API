# Compatibility policy

API contract version: **1.0.0** (`G3DoorsApi.API_VERSION`).
Only `hu.g3projectdoorsreborn.api` is a supported integration package.
Internal managers, block classes, block entities, raw block-state properties and GeckoLib
objects are not API contracts, even when their Java visibility is public.

The Forge 1.20.1 and NeoForge 1.21.1 public source signatures are identical.
Their Minecraft dependencies, mapping environments and Java bytecode targets differ.
Compile and distribute a separate integration build for each platform.

Proposed release policy:

- Patch: compatible fixes with unchanged public signatures.
- Minor: additive functions or data; preserve existing signatures and enum meanings.
- Major: intentional incompatible contract changes, announced with migration guidance.

This is a compatibility policy for maintaining the API; it is not a promise that an unreleased
local binary has already been published under a unique distribution version.

Adding a record component requires retaining its previous constructor to remain binary-compatible.
Renaming/removing accessors or changing existing parameter types is a breaking change.

Handle unknown future result/restriction values with a default path in integrations.
Do not persist enum ordinals, internal integer animation values, group UUIDs or class names.
Use registry IDs and re-resolve world positions.
Do not retain snapshots as if they were live entities.

The existing mod version has not been changed or published automatically. Before a public
release, assign a new unique mod/artifact version; do not overwrite an earlier 1.0.0 artifact.
A version string alone cannot distinguish an old JAR without the API from this local rebuild.

Optional integrations must load their API-referencing classes only when G3 Doors is present.
A ModList check after eager class loading is too late to prevent missing-class failures.

The complete implementation source can remain private. Publishing a binary JAR does expose
compiled bytecode to inspection; an API boundary is not bytecode secrecy.
Do not obfuscate the public API names/signatures. No source JAR or separate API JAR is required.

Adding a new door normally needs a private adapter/registration update and tests, not changes
to existing public methods. Consumers can continue using positions and the existing commands.
Use additive capabilities for genuinely new behavior; do not expose internal managers as API.

The documentation does not change the mod's license. No new source-redistribution permission
is implied. Confirm the intended documentation/example and binary distribution terms before
public release; the owner's final terms remain a publication task.
