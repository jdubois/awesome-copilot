---
name: java-springboot
description: 'Get on-demand best practices for developing Spring Boot applications: web, service and data layers, with pointers to detailed references for configuration, database, logging, security and testing.'
---

# Spring Boot Best Practices

Your goal is to help me write high-quality Spring Boot applications by following established best practices.

## Project Setup & Structure

- **Build Tool:** Use Maven (`pom.xml`) or Gradle (`build.gradle(.kts)`), whichever the project already uses for dependency management.
- **Starters:** Use Spring Boot starters (e.g., `spring-boot-starter-web` on Boot 3, `spring-boot-starter-webmvc` on Boot 4; `spring-boot-starter-data-jpa`) to simplify dependency management.
- **Package Structure:** Organize code by feature/domain (e.g., `com.example.app.order`, `com.example.app.user`) rather than by layer (e.g., `com.example.app.controller`, `com.example.app.service`).

## Core Rules

- **Dependency injection:** use constructor injection (single constructor, `final` fields); avoid field injection.
- **Configuration:** use `application.yml`/`.properties` with profiles, bind typed settings with `@ConfigurationProperties`, and externalize secrets (environment variables or a secret store, never committed).
- **Logging:** use SLF4J with parameterized messages (`log.info("Order {} created", id)`), never string concatenation; never log secrets or personal data.

Deeper guidance lives in the references of the `create-spring-boot-java-project` skill: `references/configuration.md` (profiles, `@ConfigurationProperties`, secrets), `references/database.md` (PostgreSQL, Hibernate, Flyway/Liquibase), `references/logging.md`, `references/security.md` and `references/spring-boot-4.md` (Boot 3 vs 4 differences). The always-on `springboot` instruction file covers the same rules.

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
- For authentication, authorization, OAuth2/JWT, method security, CSRF/CORS: use the `spring-security` skill. Basics when scaffolding: `references/security.md` in the `create-spring-boot-java-project` skill.

## Observability

- Expose Actuator `health`/`info` only by default; for metrics, tracing and log correlation (Micrometer, OpenTelemetry) use the `spring-observability` skill.

See also: Actuator <https://docs.spring.io/spring-boot/reference/actuator/index.html>, testing <https://docs.spring.io/spring-boot/reference/testing/index.html>, upgrading <https://docs.spring.io/spring-boot/upgrading.html> (for a Boot 4 move use `spring-boot-4-migration`).
