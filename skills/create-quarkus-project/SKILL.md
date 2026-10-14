---
name: create-quarkus-project
description: 'Create a new Quarkus project (Maven or Gradle) with the Quarkus CLI, code.quarkus.io API or Maven plugin, choose extensions, and run it in dev mode. Use when asked to scaffold, bootstrap or start a Quarkus application or REST API.'
---

# Create a Quarkus Project

## 1. Detect the environment

```bash
java -version          # Java 21+ recommended, 17 minimum
quarkus --version      # optional Quarkus CLI
./mvnw -v || mvn -v    # or ./gradlew -v
```

If the folder already contains a Quarkus project (`io.quarkus.platform` BOM, `quarkus-maven-plugin`, or `io.quarkus` Gradle plugin), do NOT scaffold: read its Quarkus version and keep it. For a new project, use the latest 3.x LTS stream (check https://quarkus.io/support/); do not invent a patch version, let the tool pick the current one.

## 2. Scaffold (pick one)

Quarkus CLI (install via https://quarkus.io/get-started/ , e.g. SDKMAN `sdk install quarkus`):

```bash
quarkus create app com.example:demo \
  --extension='rest-jackson,hibernate-orm-panache,jdbc-postgresql,hibernate-validator,smallrye-health' \
  --java=21 --maven        # or --gradle
```

No CLI: Maven plugin (pin the version to the current release from https://quarkus.io/guides/maven-tooling, shown here as `${quarkus.version}`):

```bash
mvn io.quarkus.platform:quarkus-maven-plugin:${quarkus.version}:create \
  -DprojectGroupId=com.example -DprojectArtifactId=demo \
  -Dextensions='rest-jackson,hibernate-orm-panache,jdbc-postgresql'
```

Or generate from the https://code.quarkus.io web UI / REST API (`https://code.quarkus.io/d?g=com.example&a=demo&e=rest-jackson&b=MAVEN`) and unzip.

## 3. Choose extensions

| Need | Extension |
|---|---|
| REST + JSON | `rest-jackson` (Quarkus REST; not legacy `resteasy`) |
| Database ORM | `hibernate-orm-panache`, `jdbc-postgresql` |
| Validation | `hibernate-validator` |
| Health / metrics | `smallrye-health`, `micrometer-registry-prometheus` |
| Auth | `oidc` or `smallrye-jwt` |
| OpenAPI | `smallrye-openapi` |
| REST client | `rest-client-jackson` |
| Container | `container-image-jib` (or `container-image-docker`) |

Add later with `quarkus extension add <name>` or `./mvnw quarkus:add-extension -Dextensions=<name>`; `quarkus extension list --installable` lists options.

## 4. Layout and run

```
src/main/java/...          # resources, services, entities
src/main/resources/application.properties
src/main/docker/           # Dockerfile.jvm, Dockerfile.native, ...
src/test/java/...          # @QuarkusTest / @QuarkusIntegrationTest
```

```bash
./mvnw quarkus:dev         # or: quarkus dev / ./gradlew quarkusDev
```

Dev mode gives live reload, Dev UI at http://localhost:8080/q/dev-ui, Dev Services (auto containers), and continuous testing (press `r`). Package: `./mvnw package` (runnable `target/quarkus-app/quarkus-run.jar`).

## Conventions for generated code

- Constructor injection, `@ApplicationScoped` beans, records for DTOs, `@ConfigMapping` for grouped config.
- Keep the wrapper (`mvnw`/`gradlew`) committed; keep versions in the BOM.

## References

- [Configuration](references/configuration.md)
- [Database (Dev Services, Hibernate, Flyway/Liquibase)](references/database.md)
- [Security basics](references/security.md)
- [Container images](references/container-image.md)
- [Local runtime insights (BootUI)](references/local-runtime-insights.md)

Related skills: `quarkus-dev-services-and-testing`, `quarkus-native-and-container`.
