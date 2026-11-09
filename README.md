# G3 Doors Java API

Public API **1.0.0**, bundled directly in the G3 Doors mod JAR. No separate API JAR is needed.

Supported builds:

| Platform | Minecraft | Loader | Java |
| --- | --- | --- | --- |
| Forge | 1.20.1 | 47.4.10 | 17 |
| NeoForge | 1.21.1 | 21.1.221 | 21 |

The same public package and method signatures are provided on both platforms:
`hu.g3projectdoorsreborn.api`.
Use a separate G3 Doors binary for each platform/Minecraft version.

## Documentation

- [Usage and outcomes](API-USAGE.md)
- [Forge Gradle setup](GRADLE-FORGE.md)
- [NeoForge Gradle setup](GRADLE-NEOFORGE.md)
- [Supported doors and limitations](SUPPORTED-DOORS.md)
- [Compatibility and versioning](COMPATIBILITY.md)
- [Verification and manual test checklist](TESTING.md)
- [Java integration examples](examples/DoorApiExamples.java)
- [In-game API Tester and test procedure](TESTER.md)
- [Legacy command-only smoke fixture](testmod/README.md)
- [Publication checklist](PUBLICATION.md)
- [License and usage terms](LICENSE.md)

This folder is intended for publication independently of the proprietary mod source.
It contains documentation and consumer examples, not the mod implementation.
No remote Maven repository has been published or configured by this work.
The Gradle guides use verified local full-JAR dependencies; no remote G3 coordinates are required.
The current local rebuilds still use mod version 1.0.0; distinguish them from old API-less builds
by their tested hashes and assign a unique version before releasing a new public mod artifact.
No GitHub repository, release, CurseForge/Modrinth file or Maven publication was created by this work.
The mod's existing license is not changed by this documentation.
