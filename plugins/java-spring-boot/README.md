# java-spring-boot

Spring Boot skills on top of core Java: project creation, best practices, testing, Spring Security, observability and Boot 4 migration.

## Installation

```bash
copilot plugin install java-spring-boot@awesome-copilot
```

## Included skills

| Skill | Details |
|---|---|
| `create-spring-boot-java-project` | see `skills/create-spring-boot-java-project/SKILL.md` |
| `java-add-graalvm-native-image-support` | see `skills/java-add-graalvm-native-image-support/SKILL.md` |
| `java-debugging` | see `skills/java-debugging/SKILL.md` |
| `java-development` | see `skills/java-development/SKILL.md` |
| `java-diagnostics` | see `skills/java-diagnostics/SKILL.md` |
| `java-hibernate-jpa` | see `skills/java-hibernate-jpa/SKILL.md` |
| `java-junit` | see `skills/java-junit/SKILL.md` |
| `java-springboot` | see `skills/java-springboot/SKILL.md` |
| `java-testcontainers` | see `skills/java-testcontainers/SKILL.md` |
| `java-upgrade` | see `skills/java-upgrade/SKILL.md` |
| `javax-to-jakarta-migration` | see `skills/javax-to-jakarta-migration/SKILL.md` |
| `lsp-setup` | see `skills/lsp-setup/SKILL.md` |
| `spring-boot-4-migration` | see `skills/spring-boot-4-migration/SKILL.md` |
| `spring-boot-testing` | see `skills/spring-boot-testing/SKILL.md` |
| `spring-observability` | see `skills/spring-observability/SKILL.md` |
| `spring-security` | see `skills/spring-security/SKILL.md` |

This plugin bundles the core Java skills it relies on, so `java-development` is not required.

## Which skill to use when

| Need | Skill |
|---|---|
| Create a new Spring Boot project | `create-spring-boot-java-project` |
| Spring Boot application code (data, REST, messaging, caching, config, AOT) | `java-springboot` |
| Everyday plain-Java work (build, dependencies, idioms, quality) | `java-development` |
| JPA/Hibernate mapping, queries, schema management | `java-hibernate-jpa` |
| Spring Boot tests (slices, Testcontainers) | `spring-boot-testing` |
| Generic Testcontainers lifecycle and reuse | `java-testcontainers` |
| Authentication and authorization | `spring-security` |
| Actuator, metrics, tracing, heapdump policy | `spring-observability` |
| Spring Boot 3.x to 4 migration | `spring-boot-4-migration` |
| Other Java/Boot version upgrades | `java-upgrade` |
| Explicit javax to jakarta namespace migration | `javax-to-jakarta-migration` |
| Native image for Spring Boot | `java-add-graalvm-native-image-support` |
| JVM diagnostics | `java-diagnostics` |

## Source

Part of [Awesome Copilot](https://github.com/github/awesome-copilot).

## License

MIT
