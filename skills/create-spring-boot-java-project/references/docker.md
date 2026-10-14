# Docker

Two image flavours are supported in this first pass: a **JVM image** ([../assets/Dockerfile](../assets/Dockerfile)) and a **GraalVM native image** ([../assets/Dockerfile-native](../assets/Dockerfile-native)). Both are Maven-based multi-stage builds; copy the one you need to the project root as `Dockerfile` (for the native one, `Dockerfile-native` and `-f Dockerfile-native`). For Gradle, replace the Maven build commands with `./gradlew bootJar` / `./gradlew nativeCompile` and the jar/executable paths under `build/libs` / `build/native/nativeCompile`. AOT and CRaC images are deferred.

## Choosing

| | JVM image | Native image |
|--|-----------|--------------|
| Build time | Fast | Slow, memory hungry (several GB) |
| Startup / memory | Seconds / higher | Milliseconds / low |
| Compatibility | Everything | Needs GraalVM-compatible libraries; reflection hints may be needed |
| Pick when | Default choice | Scale-to-zero, CLI-like, tight memory limits |

Native requires the `native` dependency at generation time (`-d dependencies=...,native`) and the Spring Boot `native` Maven profile (included by start.spring.io when the dependency is selected).

## Properties of the provided Dockerfiles

- Multi-stage: build in a JDK/GraalVM image, run in a small runtime image.
- JVM: the Boot jar is extracted into layers (`java -Djarmode=tools -jar app.jar extract --layers --launcher`) for better caching, runtime assembled with `jlink`, final base is distroless `nonroot` (no shell, no package manager).
- Native: static-ish executable copied into distroless `nonroot`.
- Run as non-root, expose 8080, JVM flags via `JAVA_TOOL_OPTIONS` (no shell to expand `JAVA_OPTS`).
- Pinned base image tags (`eclipse-temurin:25-jdk-noble`, `ghcr.io/graalvm/graalvm-community:25`, `gcr.io/distroless/base-debian12:nonroot`); match the Java version of the project and update when it changes. Consider pinning by digest for reproducibility.
- No Docker `HEALTHCHECK` (distroless has no curl); probe `/actuator/health` from the orchestrator (liveness/readiness: `management.endpoint.health.probes.enabled=true`).
- Keep a `.dockerignore` (`target/`, `build/`, `.git`, `.env`, IDE files) so secrets and build output stay out of the context.

## Build and run

```bash
docker build -t my-spring-app .
docker run --rm -p 8080:8080 \
  -e SPRING_DATASOURCE_URL=jdbc:postgresql://host.docker.internal:5432/mydb \
  -e SPRING_DATASOURCE_USERNAME=user -e SPRING_DATASOURCE_PASSWORD=password \
  my-spring-app
```

Alternative without a Dockerfile: `./mvnw spring-boot:build-image` or `./gradlew bootBuildImage` (Cloud Native Buildpacks; add `-Pnative` for native).

## Development

Use `spring-boot-docker-compose` with `compose.yaml` for dependencies (see [database.md](database.md)); do not containerize the application itself during development.
