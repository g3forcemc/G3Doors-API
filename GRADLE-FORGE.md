# Forge 1.20.1: local JAR dependency

Verified: Java 17, Forge 47.4.10, ForgeGradle 6.0.54. Start with the Forge 1.20.1 MDK.
No public G3 Maven repository is needed.

Download the **Forge 1.20.1, API-bearing** full G3 Doors JAR from its official distribution
(CurseForge or Modrinth when that release is available), plus GeckoLib Forge 4.8.3.
Not every older 1.0.0 download contains the API. See [tested binaries](TESTING.md).
Place both in the integration project's `libs` directory:

```text
libs/
  g3project_doors_reborn-1.0.0.jar
  geckolib-forge-1.20.1-4.8.3.jar
```

If downloaded filenames differ, rename local copies or adjust the selectors below.
Renaming does not change embedded versions, Minecraft compatibility or API contents.
Do not use a NeoForge, sources or development JAR.

## build.gradle additions

```groovy
repositories {
    flatDir { dirs 'libs' }
}

dependencies {
    minecraft 'net.minecraftforge:forge:1.20.1-47.4.10'
    implementation fg.deobf('hu.g3projectdoorsreborn:g3project_doors_reborn:1.0.0')
    implementation fg.deobf('software.bernie.geckolib:geckolib-forge-1.20.1:4.8.3')
}

tasks.named('jar', Jar) { finalizedBy 'reobfJar' }
```

Retain the MDK's plugins, mappings and runs; add each entry only once. These selectors resolve
**local filenames through flatDir**, not published G3 Maven coordinates. The group is ignored
by flat-directory resolution. `fg.deobf` remaps production Forge dependencies for development.
The output integration must be reobfuscated for installation.

Local JARs have no POM supplying transitive dependencies, so GeckoLib is explicit.
For development runs with GeckoLib's production refmap the verified tester also uses:

```groovy
minecraft.runs.configureEach {
    property 'mixin.env.remapRefMap', 'true'
    property 'mixin.env.refMapRemappingFile', file('build/createSrgToMcp/output.srg').absolutePath
}
```

These properties affect development runs only; they do not patch G3 Doors or belong in the
player's launcher settings.

## Mandatory META-INF/mods.toml dependency

For an integration with mod ID `my_integration`:

```toml
[[dependencies.my_integration]]
modId="g3project_doors_reborn"
mandatory=true
versionRange="[1.0.0,)"
ordering="AFTER"
side="BOTH"
```

Use your own mod ID in the table name. This is the current local tester's minimum; before
public release use the actual uniquely versioned API-bearing release. Version checks alone
cannot reject an old API-less 1.0.0 with the same version. Retain Forge/Minecraft declarations.

Run `gradlew.bat build`. Use `--offline` only after caching Gradle/Forge dependencies.
No G3 source checkout or separate API JAR is required. Do not shade/jarJar G3 Doors, GeckoLib
or copied API classes. Players install the integration, matching G3 Doors and GeckoLib in
`mods`; Gradle does not install dependencies for players.

Official reference: [ForgeGradle local dependencies](https://docs.minecraftforge.net/en/fg-6.x/dependencies/).
