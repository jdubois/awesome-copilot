# Spring Boot native images

Spring Boot 3.x and later supports GraalVM native images through Spring AOT. Check the Spring Boot version in the build: 3.x needs Java 17+ and a GraalVM matching the Spring Boot docs for that release; always confirm the exact supported GraalVM version in the reference docs for your Spring Boot version (https://docs.spring.io/spring-boot/reference/packaging/native-image/index.html).

## How it works

- The Spring Boot Maven plugin / Gradle plugin run **AOT processing** at build time, generating bean definitions and runtime hints.
- `spring-boot-starter-parent` defines a `native` profile that applies the Native Build Tools plugin, so you normally add nothing to the POM.
- With Gradle, applying the `org.graalvm.buildtools.native` plugin next to `org.springframework.boot` activates AOT tasks (`processAot`, `processTestAot`).
- Without the Spring parent (custom parent / BOM import), add the Native Build Tools plugin yourself, following `build-tools.md`, and declare `process-aot` for the Spring Boot plugin.

## Commands

| Goal | Maven | Gradle |
|---|---|---|
| Native executable | `./mvnw -Pnative native:compile` | `./gradlew nativeCompile` |
| Native container image (Cloud Native Buildpacks, needs Docker) | `./mvnw -Pnative spring-boot:build-image` | `./gradlew bootBuildImage` |
| Native tests | `./mvnw -PnativeTest test` | `./gradlew nativeTest` |
| Run AOT on the JVM to debug | `./mvnw spring-boot:process-aot` then run with `-Dspring.aot.enabled=true` | `./gradlew processAot` |

Note that `build-image` with buildpacks does not require a local GraalVM: the builder provides it. Set `BP_NATIVE_IMAGE_BUILD_ARGUMENTS` / `BP_JVM_VERSION` through the plugin's `<environment>` / `bootBuildImage { environment }` when you need extra arguments.

Check `spring.aot.enabled=true` on the JVM first: it surfaces most AOT problems faster than a full native build.

## Custom runtime hints

Only when libraries or your own code use reflection, resources or serialization that AOT cannot infer:

```java
import org.springframework.aot.hint.MemberCategory;
import org.springframework.aot.hint.RuntimeHints;
import org.springframework.aot.hint.RuntimeHintsRegistrar;

public class MyRuntimeHints implements RuntimeHintsRegistrar {
    @Override
    public void registerHints(RuntimeHints hints, ClassLoader classLoader) {
        hints.reflection().registerType(MyClass.class,
            MemberCategory.INVOKE_DECLARED_CONSTRUCTORS,
            MemberCategory.INVOKE_DECLARED_METHODS);
        hints.resources().registerPattern("custom-config/*.properties");
        hints.serialization().registerType(MySerializableClass.class);
    }
}
```

```java
@SpringBootApplication
@ImportRuntimeHints(MyRuntimeHints.class)
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

Alternatives: `@RegisterReflectionForBinding(MyDto.class)` on a bean or configuration class for DTOs bound by Jackson, and `@Reflective` on custom annotations. Test hints with `RuntimeHintsPredicates`.

## Common issues

1. **Jackson DTOs / mix-ins**: register the types with `@RegisterReflectionForBinding` or `hints.reflection()`; include mix-in classes. Records usually work without extra hints.
2. **Resources**: register non-standard resource patterns (`hints.resources().registerPattern(...)`). `application.properties`/`application.yml` are handled.
3. **Logback**: Spring's AOT supports standard Logback configs (`logback-spring.xml`). Custom Logback components may need reflection hints.
4. **Conditional beans**: `@Profile`, `@ConditionalOnProperty` and similar are evaluated at **build time** in AOT. Properties that change bean structure must be fixed when building; runtime overrides will not add or remove beans.
5. **Dynamic proxies / CGLIB**: handled by AOT for Spring components; third-party proxies need `proxy-config` hints.
6. **Libraries without metadata**: upgrade first; then use the tracing agent (see `reachability-metadata.md`).
7. **Testcontainers / test slices** in native tests: follow the Spring Boot reference for native testing; some tests are not supported natively.
