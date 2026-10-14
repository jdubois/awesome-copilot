---
description: 'Pointer to the spring-boot-4-migration skill for upgrading Spring Boot 3.x projects to 4.0'
applyTo: '**/pom.xml, **/build.gradle, **/build.gradle.kts, **/settings.gradle.kts, **/gradle/libs.versions.toml'
---

# Spring Boot 4.0 Migration

When a task upgrades Spring Boot to 4.0 (or edits the Spring Boot version in a build file), use the `spring-boot-4-migration` skill. It holds the full workflow and topic references (starters, Jackson 3, testing, configuration properties, removed APIs) for Maven and Gradle.

- Detect the current Boot version first and migrate to the latest 3.5.x before moving to 4.0.
- Run the build and tests after each migration step.
- Never read or print secrets such as `.env` files.

Get the skill from this repository: https://github.com/github/awesome-copilot/tree/main/skills/spring-boot-4-migration (install it with the GitHub Copilot CLI plugin marketplace or copy the folder to `.github/skills/spring-boot-4-migration`).
