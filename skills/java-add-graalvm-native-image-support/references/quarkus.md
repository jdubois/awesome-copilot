# Quarkus native images

For Quarkus projects use the companion skill `skills/quarkus-native-and-container`, which is the source of truth for Quarkus native builds and container images (use the Quarkus guide below for anything it does not cover).

Quick facts, framework-agnostic bits only:

- Native support is built into Quarkus; do not add the Native Build Tools plugin.
- Build: `./mvnw package -Dnative` (Maven) or `./gradlew build -Dquarkus.native.enabled=true` (Gradle, Quarkus 3.10+; older versions use `-Dquarkus.package.type=native`; verified: 3.9.5 ignores the new keys, 3.10.0 honors them). A local (non-container) build needs a matching Mandrel/GraalVM as `JAVA_HOME` (3.40.1: Mandrel 25; 3.27.6: Mandrel 23.1 on JDK 21); verified on macOS arm64 with Maven and Gradle, binary runs and `verify -Dnative` passes on the host. Add `-Dquarkus.native.container-build=true` to build in a container when no local GraalVM/Mandrel is installed (verified on 3.40.1 with Docker; on macOS/Windows the result is a Linux binary, so run it in a Linux container and expect `verify -Dnative` ITs to fail with exit code 126). Gradle also needs `-Dquarkus.package.jar.enabled=false`.
- Reflection: `@RegisterForReflection` on the class or on a holder class with `targets`; or a `reflection-config.json` in `src/main/resources` referenced via `quarkus.native.additional-build-args=-H:ReflectionConfigurationFiles=reflection-config.json`.
- Resources: `quarkus.native.resources.includes` / `excludes` in `application.properties`.
- Extra Native Image flags: `quarkus.native.additional-build-args`, with several flags comma-separated in one property (repeating the property overwrites the earlier one).
- Mandrel is the default builder distribution for Quarkus container builds; confirm the builder image for your Quarkus version in the guide.

Guide: https://quarkus.io/guides/building-native-image
