# java-quarkus

Quarkus skills on top of core Java: project creation, Dev Services and testing, native and container builds.

## Installation

```bash
copilot plugin install java-quarkus@awesome-copilot
```

## Included skills

| Skill | Details |
|---|---|
| `create-quarkus-project` | see `skills/create-quarkus-project/SKILL.md` |
| `java-add-graalvm-native-image-support` | see `skills/java-add-graalvm-native-image-support/SKILL.md` |
| `java-debugging` | see `skills/java-debugging/SKILL.md` |
| `java-development` | see `skills/java-development/SKILL.md` |
| `java-diagnostics` | see `skills/java-diagnostics/SKILL.md` |
| `java-hibernate-jpa` | see `skills/java-hibernate-jpa/SKILL.md` |
| `java-junit` | see `skills/java-junit/SKILL.md` |
| `java-testcontainers` | see `skills/java-testcontainers/SKILL.md` |
| `java-upgrade` | see `skills/java-upgrade/SKILL.md` |
| `javax-to-jakarta-migration` | see `skills/javax-to-jakarta-migration/SKILL.md` |
| `lsp-setup` | see `skills/lsp-setup/SKILL.md` |
| `quarkus-application-development` | see `skills/quarkus-application-development/SKILL.md` |
| `quarkus-dev-services-and-testing` | see `skills/quarkus-dev-services-and-testing/SKILL.md` |
| `quarkus-native-and-container` | see `skills/quarkus-native-and-container/SKILL.md` |

This plugin bundles the core Java skills it relies on, so `java-development` is not required.

## Which skill to use when

| Need | Skill |
|---|---|
| Create a new Quarkus project | `create-quarkus-project` |
| Write Quarkus code (REST, config, CDI, persistence, reactive, messaging, security, observability) | `quarkus-application-development` |
| Everyday plain-Java work (build, dependencies, idioms, quality) | `java-development` |
| Dev Services and Quarkus tests | `quarkus-dev-services-and-testing` |
| Native executables and container images | `quarkus-native-and-container` |
| JPA/Hibernate mapping, queries, schema management | `java-hibernate-jpa` |
| Generic Testcontainers (outside Dev Services) | `java-testcontainers` |
| Java/Quarkus version upgrades | `java-upgrade` |
| Explicit javax to jakarta namespace migration | `javax-to-jakarta-migration` |
| JVM diagnostics | `java-diagnostics` |

`java-add-graalvm-native-image-support` dispatches Quarkus projects to `quarkus-native-and-container`.

## Source

Part of [Awesome Copilot](https://github.com/github/awesome-copilot).

## License

MIT
