# Database

Extensions: `hibernate-orm-panache`, `jdbc-postgresql` (or another JDBC driver). Optional: `flyway` or `liquibase`.

## Dev Services

With no `quarkus.datasource.jdbc.url` configured, dev and test modes start a PostgreSQL container automatically (Docker/Podman required). Do not set the URL in `%dev`/`%test`. Tuning:

```properties
quarkus.datasource.db-kind=postgresql
%dev.quarkus.datasource.devservices.image-name=postgres:17
%dev.quarkus.datasource.devservices.reuse=true
```

Production settings go in `%prod.`:

```properties
%prod.quarkus.datasource.jdbc.url=${DB_URL}
%prod.quarkus.datasource.username=${DB_USER}
%prod.quarkus.datasource.password=${DB_PASSWORD}
```

## Hibernate ORM

```properties
%dev.quarkus.hibernate-orm.schema-management.strategy=drop-and-create
%prod.quarkus.hibernate-orm.schema-management.strategy=none
```

(Older 3.x releases name this `quarkus.hibernate-orm.database.generation`; check the Hibernate ORM guide for the project's version.)

Panache repository:

```java
@Entity
public class Product { @Id @GeneratedValue Long id; String name; }

@ApplicationScoped
public class ProductRepository implements PanacheRepository<Product> {}
```

Put `@Transactional` on methods that write. Use `find(...).page(Page.of(0, 20))` for pagination.

## Migrations

For production use Flyway (`quarkus-flyway`) or Liquibase (`quarkus-liquibase`):

```properties
quarkus.flyway.migrate-at-start=true
```

Place scripts in `src/main/resources/db/migration` (`V1__init.sql`). Flyway also gets a Dev Services-provided datasource automatically.
