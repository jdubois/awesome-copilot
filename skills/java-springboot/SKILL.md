---
name: java-springboot
description: 'Get on-demand best practices for developing Spring Boot applications: web, service and data layers, with pointers to detailed references for configuration, database, logging, security and testing.'
---

# Spring Boot Best Practices

Your goal is to help me write high-quality Spring Boot applications by following established best practices.

## Project Setup & Structure

- **Build Tool:** Use Maven (`pom.xml`) or Gradle (`build.gradle(.kts)`), whichever the project already uses for dependency management.
- **Starters:** Use Spring Boot starters (e.g., `spring-boot-starter-web`, `spring-boot-starter-data-jpa`) to simplify dependency management.
- **Package Structure:** Organize code by feature/domain (e.g., `com.example.app.order`, `com.example.app.user`) rather than by layer (e.g., `com.example.app.controller`, `com.example.app.service`).

## Core Rules (see the always-on instruction)

Dependency injection, configuration basics, logging and basic input handling are defined in [instructions/springboot.instructions.md](../../instructions/springboot.instructions.md); they are not repeated here. Deeper guidance:

- Configuration (profiles, `@ConfigurationProperties`, secrets): [configuration](../create-spring-boot-java-project/references/configuration.md)
- Database (PostgreSQL, Hibernate settings, Flyway/Liquibase): [database](../create-spring-boot-java-project/references/database.md)
- Logging (levels, structured logging): [logging](../create-spring-boot-java-project/references/logging.md)
- Security basics: [security](../create-spring-boot-java-project/references/security.md)
- Boot 3 vs 4 differences: [spring-boot-4](../create-spring-boot-java-project/references/spring-boot-4.md)

## Web Layer (Controllers)

- **RESTful APIs:** Design clear and consistent RESTful endpoints.
- **DTOs (Data Transfer Objects):** Use DTOs to expose and consume data in the API layer. Do not expose JPA entities directly to the client.
- **Validation:** Use Java Bean Validation (JSR 380) with annotations (`@Valid`, `@NotNull`, `@Size`) on DTOs to validate request payloads.
- **Error Handling:** Implement a global exception handler using `@ControllerAdvice` and `@ExceptionHandler` to provide consistent error responses.

## Service Layer

- **Business Logic:** Encapsulate all business logic within `@Service` classes.
- **Statelessness:** Services should be stateless.
- **Transaction Management:** Use `@Transactional` on service methods to manage database transactions declaratively. Apply it at the most granular level necessary.

## Data Layer (Repositories)

- **Spring Data JPA:** Use Spring Data JPA repositories by extending `JpaRepository` or `CrudRepository` for standard database operations.
- **Custom Queries:** For complex queries, use `@Query` or the JPA Criteria API.
- **Projections:** Use DTO projections to fetch only the necessary data from the database.

## Testing

- Use the `spring-boot-testing` skill for slices (`@WebMvcTest`, `@DataJpaTest`), Testcontainers with `@ServiceConnection`, the Failsafe `*IT` gotcha, and Boot 3 vs 4 / JUnit 5 vs 6 compatibility.
- Plain JUnit + Mockito for services; `@SpringBootTest` only for real integration tests.

## Security

- Use Spring Security; always hash passwords (BCrypt or the delegating encoder) and use parameterized queries.
- For authentication, authorization, OAuth2/JWT, method security, CSRF/CORS: use the `spring-security` skill. Basics when scaffolding: [security](../create-spring-boot-java-project/references/security.md).

## Observability

- Expose Actuator `health`/`info` only by default; for metrics, tracing and log correlation (Micrometer, OpenTelemetry) use the `spring-observability` skill.
