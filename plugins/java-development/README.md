# java-development

Core Java skills for any Java project: JUnit, Javadoc, Hibernate/JPA, Testcontainers, debugging and JVM diagnostics, Java upgrades, Jakarta migration, GraalVM native image, refactoring and LSP setup.

## Installation

```bash
copilot plugin install java-development@awesome-copilot
```

## Included skills

| Skill | Details |
|---|---|
| `java-add-graalvm-native-image-support` | see `skills/java-add-graalvm-native-image-support/SKILL.md` |
| `java-debugging` | see `skills/java-debugging/SKILL.md` |
| `java-development` | see `skills/java-development/SKILL.md` |
| `java-diagnostics` | see `skills/java-diagnostics/SKILL.md` |
| `java-docs` | see `skills/java-docs/SKILL.md` |
| `java-hibernate-jpa` | see `skills/java-hibernate-jpa/SKILL.md` |
| `java-junit` | see `skills/java-junit/SKILL.md` |
| `java-refactoring-extract-method` | see `skills/java-refactoring-extract-method/SKILL.md` |
| `java-refactoring-remove-parameter` | see `skills/java-refactoring-remove-parameter/SKILL.md` |
| `java-testcontainers` | see `skills/java-testcontainers/SKILL.md` |
| `java-upgrade` | see `skills/java-upgrade/SKILL.md` |
| `javax-to-jakarta-migration` | see `skills/javax-to-jakarta-migration/SKILL.md` |
| `lsp-setup` | see `skills/lsp-setup/SKILL.md` |

## Which skill to use when

| Need | Skill |
|---|---|
| Everyday plain-Java work (build, dependencies, modern idioms, logging, quality, packaging, CI) | `java-development` |
| Write or fix JUnit tests | `java-junit` |
| Javadoc | `java-docs` |
| JPA/Hibernate mapping, queries, schema management | `java-hibernate-jpa` |
| Integration tests with Testcontainers | `java-testcontainers` |
| Debug a failing program | `java-debugging` |
| JVM diagnostics (CPU, memory, threads) | `java-diagnostics` |
| Upgrade Java or dependency versions | `java-upgrade` |
| Explicit javax to jakarta namespace migration | `javax-to-jakarta-migration` |
| Native image (Spring Boot, Micronaut, plain Java) | `java-add-graalvm-native-image-support` |
| Extract method / remove parameter refactoring | `java-refactoring-extract-method, java-refactoring-remove-parameter` |
| Language server setup | `lsp-setup` |

## Migration note

Version 2.0.0 contains core Java skills only. `create-spring-boot-java-project` and `java-springboot` moved to the `java-spring-boot` plugin.

## Source

Part of [Awesome Copilot](https://github.com/github/awesome-copilot).

## License

MIT
