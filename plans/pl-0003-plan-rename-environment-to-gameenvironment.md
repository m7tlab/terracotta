+++
id = "PL-003"
title = "Plan: Rename `environment` to `gameEnvironment`"
status = "open"
priority = "medium"
+++

# Plan: Rename `environment` to `gameEnvironment`

## Motivation

The field name `environment` is confusing because it collides with the common concept of environment variables (envvars). Renaming to `gameEnvironment` makes it unambiguous that this refers to the Minecraft runtime environment (client/server/universal), not system environment variables.

## Scope

This is a **breaking change** affecting the public API (Kotlin DSL, YAML config, Gradle plugin DSL). The rename covers:

- Kotlin source: enum class, properties, parameters, method names, local variables
- Gradle plugin DSL: `TerracottaExtension.environment` → `gameEnvironment`
- YAML config key: `environment:` → `gameEnvironment:`
- Tests: all references to the old names
- Documentation: all `.md` files referencing the field
- KDoc comments mentioning "environment" in the game-environment sense

**Not in scope** (these use "environment" in the envvar/runtime sense and must NOT be renamed):
- `System.getenv()` / environment variable references
- `GradleRunner.withEnvironment(env)` in tests
- GitHub Actions `environment:` key in workflow YAML
- Doc text like "environment variables", "non-interactive environments", "local environment"
- `SECURITY.md` "secure online environment"
- CHANGELOG entries (per repo rules — don't modify previous entries)

## Rename mapping

| Old | New |
|-----|-----|
| `TerracottaEnvironment` (class name) | `TerracottaGameEnvironment` |
| `environment` (property/parameter/variable) | `gameEnvironment` |
| `detectEnvironment()` (method) | `detectGameEnvironment()` |
| `environment:` (YAML config key) | `gameEnvironment:` |
| `"## Environment values"` (doc heading) | `"## Game environment values"` |
| KDoc "environment" (game sense) | "game environment" |

## Files to modify

### Core module — source

1. **`TerracottaEnvironment.kt`** → rename file to `TerracottaGameEnvironment.kt`
   - Rename enum class, update KDoc, update `fromId` error messages
2. **`TerracottaVersion.kt`** — `environment` property → `gameEnvironment`, update import
3. **`ProjectMetadata.kt`** — `environment` property → `gameEnvironment`, update KDoc, update import
4. **`AbstractProjectMetadata.kt`** — `environment` property → `gameEnvironment`, update all references in `merge`, `equals`, `hashCode`, `toString`, update import
5. **`TerracottaProjectMetadata.kt`** — `environment` parameter → `gameEnvironment`, update import
6. **`TerracottaLoader.kt`** — `detectEnvironment()` → `detectGameEnvironment()`, update KDoc, update import
7. **`AbstractTerracottaLoader.kt`** — `detectEnvironment()` → `detectGameEnvironment()`, update import
8. **`FabricLoader.kt`** — `detectEnvironment()` → `detectGameEnvironment()`, update import. **Note:** `json["environment"]` reads the Fabric mod JSON key — this is an external file format field and must NOT be renamed.
9. **`BukkitLoader.kt`** — `detectEnvironment()` → `detectGameEnvironment()`, update import
10. **`LoaderMetadataDetector.kt`** — `detectEnvironment()` private method → `detectGameEnvironment()`, `environment =` → `gameEnvironment =`, update import
11. **`TerracottaConfig.kt`** — `environment` property → `gameEnvironment`, update KDoc
12. **`TerracottaConfigLoader.kt`** — `map.readString("environment")` → `map.readString("gameEnvironment")`, `environment =` → `gameEnvironment =`
13. **`ResolvedProjectMetadata.kt`** — `environment` property → `gameEnvironment`, update import
14. **`ProjectMetadataResolver.kt`** — `config.environment` → `config.gameEnvironment`, `detected.environment` → `detected.gameEnvironment`, update import

### Core module — tests

15. **`TerracottaEnvironmentTest.kt`** → rename file to `TerracottaGameEnvironmentTest.kt`, update class name and all references
16. **`EnumFromIdPropertyTest.kt`** — update all `TerracottaEnvironment` references
17. **`TerracottaProjectMetadataTest.kt`** — `environment =` → `gameEnvironment =`, `metadata.environment` → `metadata.gameEnvironment`, update import
18. **`ProjectMetadataResolverTest.kt`** — `result.environment` → `result.gameEnvironment`, update import
19. **`TerracottaConfigLoaderTest.kt`** — YAML key `environment:` → `gameEnvironment:`, `config.environment` → `config.gameEnvironment`
20. **`OperationPreprocessorTest.kt`** — `environment =` → `gameEnvironment =`, `processed.environment` → `processed.gameEnvironment`, update import
21. **`OperationPreprocessorPropertyTest.kt`** — `environment =` → `gameEnvironment =`, update import
22. **`ProjectMetadataLoaderTest.kt`** — update import and any `environment` references

### Gradle plugin module — source

23. **`TerracottaExtension.kt`** — `environment` property → `gameEnvironment`, update import
24. **`TerracottaTaskRegistrar.kt`** — `task.environment.set(extension.environment)` → `task.gameEnvironment.set(extension.gameEnvironment)` (2 places)
25. **`TerracottaExtensionConfigurer.kt`** — `extension.environment.convention(resolved.environment)` → `extension.gameEnvironment.convention(resolved.gameEnvironment)`
26. **`TerracottaApplyTask.kt`** — `environment` property → `gameEnvironment`, update import, update `environment = environment.get()` → `gameEnvironment = gameEnvironment.get()`
27. **`TerracottaPlanTask.kt`** — same as above

### Gradle plugin module — tests

28. **`TerracottaPluginIntegrationTest.kt`** — YAML key `environment:` → `gameEnvironment:` (2 places)

### Provider modules — source

29. **`ModrinthClient.kt`** — local var `environment` → `gameEnvironment`, `project.versions.firstOrNull()?.environment` → `?.gameEnvironment`, extension functions `TerracottaEnvironment.toModrinthClientSide/ServerSide` → `TerracottaGameEnvironment...`, update import
30. **`HangarClient.kt`** — `version.environment` → `version.gameEnvironment`, update import, update warn message
31. **`HangarStateProvider.kt`** — `environment = TerracottaEnvironment.SERVER_ONLY` → `gameEnvironment = TerracottaGameEnvironment.SERVER_ONLY`, update import

### Provider modules — tests

32. **`ModrinthClientTest.kt`** — `environment =` → `gameEnvironment =`, local var `environment` → `gameEnvironment`, update import, update test name
33. **`HangarProviderTest.kt`** — `environment =` → `gameEnvironment =`, update import, update test name

### Documentation

34. **`docs/content/modules/core/reference/models.md`** — `environment` → `gameEnvironment` in table, `TerracottaEnvironment` → `TerracottaGameEnvironment` in heading and text
35. **`docs/content/modules/core/reference/metadata-resolution.md`** — `environment` → `gameEnvironment` in table
36. **`docs/content/modules/core/reference/config-schema.md`** — `environment` → `gameEnvironment` in config table, YAML example, detection table; rename heading "Environment values" → "Game environment values"
37. **`docs/content/modules/core/explanation/metadata-resolution.md`** — `environment` → `gameEnvironment` (game-sense references only)
38. **`docs/content/modules/gradle-plugin/how-to-guides/kotlin-dsl-configuration.md`** — `environment.set(TerracottaEnvironment.SERVER_ONLY)` → `gameEnvironment.set(TerracottaGameEnvironment.SERVER_ONLY)`, update import
39. **`docs/content/modules/provider-hangar/explanation/architecture.md`** — `CLIENT_ONLY` environments → `CLIENT_ONLY` game environments (clarify wording)
40. **`docs/content/modules/gradle-plugin/tutorials/getting-started.md`** — check for `environment:` YAML key references

### Project files

41. **`project/TODO.md`** — remove the "Rename `environment` to `gameEnvironment`" line (done)

## Execution order

1. **Core model layer** (files 1–6): rename enum, properties, method signatures
2. **Core implementation layer** (files 7–14): update all implementations and config
3. **Core tests** (files 15–22): update all test references
4. **Gradle plugin source** (files 23–27): update DSL and tasks
5. **Gradle plugin tests** (file 28): update test YAML
6. **Provider source** (files 29–31): update provider clients
7. **Provider tests** (files 32–33): update provider tests
8. **Documentation** (files 34–40): update all docs
9. **Project files** (file 41): mark TODO as done
10. **Verify**: `./gradlew build` — compile and run all tests

## Special care

- **`FabricLoader.kt` line 26**: `json["environment"]` reads the `fabric.mod.json` external file format. This key must remain `"environment"` — it's the Fabric mod descriptor's field name, not our API.
- **`TerracottaConfigLoader.kt`**: `map.readString("environment")` reads the YAML config key. This MUST be renamed to `"gameEnvironment"` since it's our config schema.
- **Test YAML strings**: The `environment: server_only` in test YAML strings must become `gameEnvironment: server_only`.
- **CHANGELOG.md**: Do not modify previous entries. Add a new entry under "Unreleased" documenting the breaking rename.
- **`@SerialName` annotations**: The serialized enum values (`client_only`, `server_only`, `universal`) stay the same — only the Kotlin identifiers change.
