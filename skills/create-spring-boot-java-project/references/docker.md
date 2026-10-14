# Docker

Four multi-stage Dockerfiles are provided: JVM and GraalVM native images, each for Maven and Gradle (Groovy or Kotlin DSL). Copy the one you need to the project root and build it with `-f`. AOT and CRaC images are deferred.

| | Maven | Gradle |
|--|-------|--------|
| JVM image | [../assets/Dockerfile](../assets/Dockerfile) | [../assets/Dockerfile-gradle](../assets/Dockerfile-gradle) |
| Native image | [../assets/Dockerfile-native](../assets/Dockerfile-native) | [../assets/Dockerfile-native-gradle](../assets/Dockerfile-native-gradle) |

Verified on Boot 4.1.1 (web + actuator + validation), arm64 Docker Desktop: all four build and serve `/hello` and `/actuator/health`. JVM images are about 200 MB and build in under a minute (about 2 s startup); native images are about 175 MB, take 2m20-2m55 to build (around 6 GB of builder memory) and start in about 0.09 s. The Gradle builds set the toolchain to the same Java version as the build stage (25).

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
- Native: the executable is built with `-H:+StaticExecutableWithDynamicLibC` and copied into distroless `nonroot`. The `find` that picks the executable must skip `*.so`: GraalVM 25 leaves executable `libjava.so`/`libjvm.so` stubs next to it, and copying one of those gives a container that exits with 139 (segfault).
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

Alternative without a Dockerfile: `./mvnw spring-boot:build-image` or `./gradlew bootBuildImage` (Cloud Native Buildpacks; add `-Pnative` for native with Maven, Gradle needs the `native` plugin). Verified: Maven native build-image about 2m40, 282 MB image, runs. Gradle `bootBuildImage` built a native image in 2m50 but the image crashed at startup with `Invalid logger interface org.hibernate.validator.internal.util.logging.Log (implementation not found)` when `spring-boot-starter-validation` was present (the reachability metadata packed for hibernate-validator was a 2 KB copy of the jboss-logging one); for Gradle prefer `nativeCompile` or `Dockerfile-native-gradle`, which ran fine with the same dependencies.

## Development

Use `spring-boot-docker-compose` with `compose.yaml` for dependencies (see [database.md](database.md)); do not containerize the application itself during development.
