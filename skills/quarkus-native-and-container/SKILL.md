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
# Gradle
./gradlew build -Dquarkus.native.enabled=true -Dquarkus.package.jar.enabled=false
```

Binary: `target/*-runner`. Mandrel is Red Hat's GraalVM distribution for Quarkus; pick the builder image matching your Java version via `quarkus.native.builder-image` if needed. Native builds are slow and memory-hungry (several GB); do them in CI or on demand, not on every test run.

## Common native failures

| Symptom | Fix |
|---|---|
| `ClassNotFoundException` / `NoSuchMethodException` at runtime (reflection, JSON serialization of DTOs) | annotate with `@RegisterForReflection` or list `quarkus.native.additional-build-args` / `reflection-config.json` |
| Missing resource files | `quarkus.native.resources.includes=templates/**` |
| Library not supporting native | look for a Quarkus extension, or choose another library |
| Class initialized at build time breaks | `--initialize-at-run-time=...` through `quarkus.native.additional-build-args` |
| `OutOfMemoryError` during build | raise container memory or `quarkus.native.native-image-xmx` |
| Dynamic proxies / serialization | register via annotations or extension |

```java
@RegisterForReflection(targets = ExternalDto.class)
public record ProductDto(String name) {}
```

Prefer Quarkus extensions (they register reflection automatically) over raw libraries.

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

Deploy with `quarkus.kubernetes.deploy=true` or `kubectl apply -f target/kubernetes/kubernetes.yml`. Health probes are wired automatically when `smallrye-health` is present. Review generated YAML before applying; keep secrets out of it.
