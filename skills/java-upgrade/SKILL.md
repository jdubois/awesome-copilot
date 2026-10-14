---
name: java-upgrade
description: 'Upgrade a Java project to a newer JDK (11 to 17, 17 to 21, 21 to 25) and adopt the new language and library features. Detects the current Java version from the build files, then loads only the matching per-version guide. Use when the user asks to upgrade Java, modernize Java code, or adopt records, sealed classes, pattern matching, virtual threads or other new Java features.'
---

# Java Upgrade

Help upgrade a Java project step by step and adopt new language features safely.

## Workflow

1. **Detect the current and target versions.** Read `pom.xml` (`maven.compiler.release`, `java.version`, toolchains), `build.gradle*` (`java.toolchain`, `sourceCompatibility`) and CI files. Never assume a target: ask the user if it is not stated.
2. **Upgrade one LTS at a time** (11 → 17 → 21 → 25). Build and run the tests after each step.
3. **Load only the guide you need** from the table below. Do not load all of them.
4. **Update the build first** (compiler release, plugins, dependencies that need newer versions), then adopt language features incrementally in small, reviewable changes.
5. **Verify**: `./mvnw verify` or `./gradlew build`, and fix deprecation and removal warnings before adopting new features.

## Per-version guides

| Upgrade | Guide |
|---|---|
| Java 11 → 17 | [references/java-11-to-17.md](references/java-11-to-17.md) |
| Java 17 → 21 | [references/java-17-to-21.md](references/java-17-to-21.md) |
| Java 21 → 25 | [references/java-21-to-25.md](references/java-21-to-25.md) |

## Rules

- Prefer behavior-preserving refactorings; do not change public APIs unless asked.
- Detect the project's frameworks (Spring Boot, Quarkus, Jakarta EE) and check their minimum supported Java version before raising the target.
- Do not adopt preview features unless the user opts in and the build enables them explicitly.
- For `javax.*` to `jakarta.*` namespace changes, use the `javax-to-jakarta-migration` skill.
