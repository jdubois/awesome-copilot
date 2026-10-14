# Quarkus native images

For Quarkus projects use the companion skill `skills/quarkus-native-and-container`, which is the source of truth for Quarkus native builds and container images (use the Quarkus guide below for anything it does not cover).

Quick facts, framework-agnostic bits only:

- Native support is built into Quarkus; do not add the Native Build Tools plugin.
- Build: `./mvnw package -Dnative` (Maven) or `./gradlew build -Dquarkus.native.enabled=true` (Gradle, Quarkus 3.10+; older versions use `-Dquarkus.package.type=native`). Add `-Dquarkus.native.container-build=true` to build in a container when no local GraalVM/Mandrel is installed.
- Reflection: `@RegisterForReflection` on the class or on a holder class with `targets`; or a `reflection-config.json` in `src/main/resources` referenced via `quarkus.native.additional-build-args=-H:ReflectionConfigurationFiles=reflection-config.json`.
- Resources: `quarkus.native.resources.includes` / `excludes` in `application.properties`.
- Extra Native Image flags: `quarkus.native.additional-build-args`, with several flags comma-separated in one property (repeating the property overwrites the earlier one).
- Mandrel is the default builder distribution for Quarkus container builds; confirm the builder image for your Quarkus version in the guide.

Guide: https://quarkus.io/guides/building-native-image
