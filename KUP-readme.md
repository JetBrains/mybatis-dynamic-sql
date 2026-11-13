# Building as a Kotlin user project (KUP)

The `kotlin-community/dev` branch contains infrastructure for building the project with a custom Kotlin version.

## Command

```bash
./mvnw test -Dlicense.skip=true -Dkotlin_version=2.5.20-dev-4223 -Dkotlin_repo_url=https://packages.jetbrains.team/maven/p/kt/dev -Dkotlin_language_version=2.5 -Dkotlin_api_version=2.5 -Dkotlin_additional_cli_options=-Xdetailed-perf
```

The Kotlin version, repository URL and compiler options above are examples. Replace them with the values you need.

## Properties

The properties are passed as Maven system properties (`-D`), not as Gradle properties (`-P`).

| Property                        | Effect                                                                                       |
|---------------------------------|----------------------------------------------------------------------------------------------|
| `kotlin_version`                | Kotlin compiler, Maven plugin and stdlib version. Also enables the KUP test exclusions.      |
| `kotlin_repo_url`               | Adds a `kotlin-dev` repository for dependencies and plugins.                                 |
| `kotlin_language_version`       | `-language-version` of the Kotlin compiler.                                                  |
| `kotlin_api_version`            | `-api-version` of the Kotlin compiler.                                                       |
| `kotlin_additional_cli_options` | Additional Kotlin compiler options separated by spaces, e.g. `-nowarn -Xdetailed-perf`.      |

The properties are handled by profiles in `pom.xml` (`kotlin-user-project-*`).
A profile is activated only if its property is set to a non-empty value, so an empty value is the same as not passing the property.

`-Xreport-all-warnings` and `-Xrender-internal-diagnostic-names` are always passed to the Kotlin compiler.
`-Werror` is not used.

## Kotlin repository filtering

`.mvn/maven.config` enables the Maven Resolver groupId filter, and `.mvn/rrf/groupId-kotlin-dev.txt` allows
only `org.jetbrains.kotlin` artifacts to be requested from the `kotlin-dev` repository.
This is the Maven counterpart of Gradle `exclusiveContent` (KQA-3148). Unlike Gradle, it filters by groupId only, not by version.

## Excluded tests

When `kotlin_version` is set, the tests listed in `.mvn/kup-test-excludes.txt` are excluded.
They use Testcontainers and need Docker (PostgreSQL, MariaDB, MySQL).

Do not use `-Dtest` to exclude more tests: it overrides the excludes file. Add patterns to `.mvn/kup-test-excludes.txt` instead.

## Agent requirements

- JDK 17
- Access to Maven Central and to the Kotlin repository from `kotlin_repo_url`
- Docker is not needed with the test exclusions above

## Expected result

`BUILD SUCCESS`, 0 failures (local run with Kotlin `2.5.20-dev-4223`).
