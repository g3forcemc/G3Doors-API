# Publication preparation

This directory contains the documentation, consumer examples and a legacy smoke fixture.
It can be reviewed independently of the private G3 implementation projects. Nothing has been
uploaded or published by this task.

Before the owner publishes:

- Review the README, local-JAR guides, compatibility policy and actual test limitations.
- Assign a unique version to the eventual API-bearing mod releases; current local binaries
  still share 1.0.0 with older builds. Update dependency minimums and checksums with that release.
- Decide and include the final documentation/example and binary distribution terms.
- Add the actual official distribution links only after the corresponding release exists.
- Upload normal Forge and NeoForge mod binaries as separate release assets if desired.
  A separate API JAR and private implementation sources are unnecessary.
- Keep unpublished tester projects/binaries private until separately authorized for publication.

Do not upload the private mod project trees, saves, caches, development logs, credentials or
local dependency JARs into a documentation repository. In particular, do not upload an entire
workspace. A prior archive is not automatically current; regenerate it from reviewed files.

There is no public G3 Maven repository configured here. The local-JAR instructions work without
one. A future Maven distribution requires a separate publication decision and real artifacts;
do not replace the working local instructions with unverified coordinates.
