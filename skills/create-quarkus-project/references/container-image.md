# Container images

## Extension-based (no Dockerfile)

```bash
quarkus extension add container-image-jib     # or container-image-docker
./mvnw package -Dquarkus.container-image.build=true
```

```properties
quarkus.container-image.group=myorg
quarkus.container-image.name=demo
quarkus.container-image.tag=1.0.0
# push: -Dquarkus.container-image.push=true  with quarkus.container-image.registry=ghcr.io
```

Jib needs no Docker daemon to build; the docker extension uses `src/main/docker/Dockerfile.jvm` / `Dockerfile.native`.

## Dockerfiles generated in src/main/docker

- `Dockerfile.jvm` (fast-jar layout), `Dockerfile.legacy-jar`, `Dockerfile.native`, `Dockerfile.native-micro`.

```bash
./mvnw package
docker build -f src/main/docker/Dockerfile.jvm -t myorg/demo:jvm .
docker run -p 8080:8080 myorg/demo:jvm
```

Run as non-root, pass config through env vars (`QUARKUS_DATASOURCE_JDBC_URL`, ...), and pin base-image tags. For native images and Kubernetes manifests see the `quarkus-native-and-container` skill.
