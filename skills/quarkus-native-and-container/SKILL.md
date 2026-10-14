---
name: quarkus-native-and-container
description: 'Build Quarkus native executables (Mandrel/GraalVM), fix native failures with reflection registration, test the native binary, and produce container images and Kubernetes/OpenShift manifests. Use for native, Docker, Jib or Kubernetes work in a Quarkus project.'
---

# Quarkus Native and Container

Check the project's Quarkus version first. For generic (non-Quarkus) GraalVM work, use the `java-add-graalvm-native-image-support` skill instead.

## Native build

```bash
# container build: no local GraalVM needed (Docker/Podman required)
./mvnw package -Dnative -Dquarkus.native.container-build=true
# local Mandrel/GraalVM (JAVA_HOME / GRAALVM_HOME must point to it)
./mvnw package -Dnative
# Gradle, Quarkus 3.10+ (add -Dquarkus.native.container-build=true without a local Mandrel)
./gradlew build -Dquarkus.native.enabled=true -Dquarkus.package.jar.enabled=false
# Gradle, older Quarkus
./gradlew build -Dquarkus.package.type=native
```

`quarkus.package.type` is deprecated in 3.40 (still accepted, with a warning naming `quarkus.native.enabled` and `quarkus.package.jar.*` as replacements). The new keys were verified on 3.40.1; the "3.10+" version gate was not tested. The Maven `native` profile in the generated `pom.xml` already sets `quarkus.native.enabled=true`, `quarkus.package.jar.enabled=false` and `skipITs=false` when `-Dnative` is passed.

Binary: `target/*-runner` (Maven) or `build/*-runner` (Gradle). A container build on macOS or Windows produces a Linux ELF that cannot run on the host: run it in a Linux container (for example `docker run -v "$PWD/target:/w:ro" registry.access.redhat.com/ubi9/ubi-minimal /w/<name>-runner` with the datasource passed through `QUARKUS_DATASOURCE_*` env vars), and note that `./mvnw verify -Dnative` then fails in the `*IT` with `Exit code is: '126'`. Mandrel is Red Hat's GraalVM distribution for Quarkus; the container build pulls a Mandrel builder image matching the project (3.40.1 used `quay.io/quarkus/ubi9-quarkus-mandrel-builder-image:jdk-25`, Mandrel 25.0.4.1, about 2.5 minutes including the image pull); override it with `quarkus.native.builder-image` if needed. Native builds are slow and memory-hungry (several GB); do them in CI or on demand, not on every test run.

## Common native failures

| Symptom | Fix |
|---|---|
| `ClassNotFoundException` / `NoSuchMethodException` at runtime (reflection, JSON serialization of DTOs) | annotate with `@RegisterForReflection` (or `@RegisterForReflection(targets = ...)` on a holder class for third-party types), or put a `reflection-config.json` in `src/main/resources` and set `quarkus.native.additional-build-args=-H:ReflectionConfigurationFiles=reflection-config.json` |
| Missing resource files | `quarkus.native.resources.includes=templates/**` |
| Library not supporting native | look for a Quarkus extension, or choose another library |
| Class initialized at build time breaks | `--initialize-at-run-time=...` through `quarkus.native.additional-build-args` |
| `OutOfMemoryError` during build | raise container memory or `quarkus.native.native-image-xmx` |
| Dynamic proxies / serialization | register via annotations or extension |

```java
@RegisterForReflection(targets = ExternalDto.class)
public record ProductDto(String name) {}
```

Prefer Quarkus extensions (they register reflection automatically) over raw libraries. Verified: a Panache + Quarkus REST app built natively and served JSON with a record DTO; Quarkus REST registers returned types itself, so `@RegisterForReflection` is mainly needed for types reached only reflectively (custom serialization, third-party types). The `reflection-config.json` and `--initialize-at-run-time` rows were not exercised.

## Test the native binary

```java
@QuarkusIntegrationTest
class GreetingResourceIT extends GreetingResourceTest {}
```

```bash
./mvnw verify -Dnative
```

`@QuarkusIntegrationTest` black-box tests run in the `verify` phase (Failsafe) against the native binary; no CDI injection.

## Container images

```bash
quarkus extension add container-image-jib      # or container-image-docker
./mvnw package -Dquarkus.container-image.build=true
./mvnw package -Dnative -Dquarkus.native.container-build=true -Dquarkus.container-image.build=true
```

Dockerfiles for manual builds live in `src/main/docker` (`Dockerfile.jvm`, `Dockerfile.native`, `Dockerfile.native-micro`). Set `quarkus.container-image.registry/group/name/tag`, and `-Dquarkus.container-image.push=true` to push. Use pinned base-image tags and run as non-root.

## Kubernetes / OpenShift

```bash
quarkus extension add kubernetes     # or openshift
./mvnw package
```

Manifests are generated in `target/kubernetes/kubernetes.yml` (and `.json`). Customize via properties:

```properties
quarkus.kubernetes.replicas=2
quarkus.kubernetes.ingress.expose=true
quarkus.kubernetes.env.secrets=app-secrets
quarkus.kubernetes.resources.requests.memory=128Mi
```

The generated Deployment includes liveness, readiness and startup probes (`/q/health/live`, `/q/health/ready`) and `envFrom.secretRef` for `env.secrets` (verified on 3.40.1). Deploy with `quarkus.kubernetes.deploy=true` or `kubectl apply -f target/kubernetes/kubernetes.yml`. Review generated YAML before applying; keep secrets out of it.
