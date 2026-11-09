# NeoForge 1.21.1: local JAR dependency

Verified: Java 21, NeoForge 21.1.221, ModDevGradle 2.0.141. Start with a matching NeoForge MDK.
No public G3 Maven repository is needed.

Download the **NeoForge 1.21.1, API-bearing** full G3 Doors JAR from its official distribution
(CurseForge or Modrinth when available), plus GeckoLib NeoForge 4.6.6.
Not every older 1.0.0 download contains the API. See [tested binaries](TESTING.md).
Place these in the integration project's `libs` directory:

```text
libs/
  g3project_doors_reborn-neo-1.0.0.jar
  geckolib-neoforge-1.21.1-4.6.6.jar
```

Rename local copies or adjust the selectors if filenames differ. Renaming does not change
embedded versions or platform. Do not use Forge, sources or development JARs.

## build.gradle additions

```groovy
repositories {
    flatDir { dirs 'libs' }
}

neoForge {
    version = '21.1.221'
}

dependencies {
    implementation 'hu.g3projectdoorsreborn:g3project_doors_reborn-neo:1.0.0'
    implementation 'software.bernie.geckolib:geckolib-neoforge-1.21.1:4.6.6'
}
```

Retain the MDK's plugins, runs, source-set registration and other dependencies; add entries
only once. These selectors resolve **local filenames through flatDir**, not published
G3 Maven coordinates. GeckoLib is explicit because local JARs have no POM providing transitive
dependencies. No fictitious repository is required.

Do not use ForgeGradle's `fg.deobf` or `reobfJar` here. The verified ModDevGradle consumer
uses the normal NeoForge mod JAR directly. With both local files supplied no GeckoLib remote
repository is needed. The tester additionally permits GeckoLib's official repository:

```groovy
repositories {
    maven {
        url = 'https://dl.cloudsmith.io/public/geckolib3/geckolib/maven/'
        content { includeGroup('software.bernie.geckolib') }
    }
}
```

## Mandatory META-INF/neoforge.mods.toml dependency

For an integration with mod ID `my_integration`:

```toml
[[dependencies.my_integration]]
modId="g3project_doors_reborn"
type="required"
versionRange="[1.0.0,)"
ordering="AFTER"
side="BOTH"
```

Use your own mod ID in the table name. Before public release replace the current local tester's
minimum with the actual uniquely versioned API-bearing release. Version checks alone cannot
distinguish old API-less 1.0.0 binaries from these rebuilds. Retain normal Minecraft/NeoForge
dependency declarations.

Run `gradlew.bat build`; `--offline` requires cached Gradle/NeoForge dependencies.
The full G3 JAR supplies the API. No source checkout or separate API JAR is required.
Do not copy API classes or shade/jarJar G3 Doors/GeckoLib into your integration.

Players install your integration, matching G3 Doors and GeckoLib in `mods`.
Gradle declarations are not a modpack installer.

Official reference: [ModDevGradle](https://github.com/neoforged/ModDevGradle).
