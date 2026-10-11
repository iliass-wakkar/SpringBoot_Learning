# Spring Boot Enterprise Engineering: Patterns, Architecture & Syllabus

[![Java](https://img.shields.io/badge/Java-17%20%7C%2021%20LTS-ED8B00?style=for-the-badge&logo=openjdk&logoColor=white)](https://openjdk.org/)
[![Spring Boot](https://img.shields.io/badge/Spring_Boot-3.x-6DB33F?style=for-the-badge&logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![Spring Security](https://img.shields.io/badge/Spring_Security-6.x-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white)](https://spring.io/projects/spring-security)
[![Spring Data JPA](https://img.shields.io/badge/ORM-Hibernate_%2F_JPA-59666C?style=for-the-badge&logo=hibernate&logoColor=white)](https://hibernate.org/)
[![PostgreSQL](https://img.shields.io/badge/Database-PostgreSQL-316192?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org/)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue?style=for-the-badge)](LICENSE)

A curated repository and enterprise engineering reference guide for building scalable, production-grade applications with **Spring Boot 3.x** and **Java 17/21 LTS**. This roadmap distills architectural patterns, best practices, security hardening, and database design used across production systems.

---

## 🗺️ Engineering Curriculum & Roadmap

```
┌────────────────────────────────────────────────────────┐
│                   1. Core Foundations                  │
│ IoC Container • Dependency Injection • Bean Scopes     │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│                2. REST & Web MVC Layer                 │
│ @RestController • DTO Pattern • Global Exceptions      │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│            3. Data Persistence (JPA / Hibernate)       │
│ Entities • Repositories • Transactions • HikariCP      │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│               4. Enterprise Security                   │
│ Spring Security 6 • Filter Chains • Stateless JWT      │
└───────────────────────────┬────────────────────────────┘
                            │
                            ▼
┌────────────────────────────────────────────────────────┐
│           5. Architecture & Production Readiness       │
│ Layered Hexagonal Design • Actuator • Docker Packaging │
└────────────────────────────────────────────────────────┘
```

---

## Module Breakdown

### 1. Inversion of Control (IoC) & Core Mechanics
- **Dependency Injection**: Constructor-based injection (`@RequiredArgsConstructor` / Lombok vs explicit constructors) over field injection (`@Autowired`).
- **Bean Lifecycle**: Post-construct initialization, pre-destroy cleanup, and component scanning boundaries.
- **Profiles & Configuration**: Environment-specific profiles (`application-dev.yml`, `application-prod.yml`) using `@ConfigurationProperties` type-safe binding.

### 2. REST API Design & Controller Patterns
- **DTO Isolation**: Strict separation between JPA persistent entities and presentation contracts using Java Records and mapping libraries (MapStruct).
- **Validation**: Declarative field validation with Jakarta Bean Validation (`@NotNull`, `@Size`, `@Pattern`, `@Positive`).
- **Global Error Handling**: Centralized exception interceptors using `@RestControllerAdvice` and RFC 7807 Problem Details:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<ProblemDetail> handleNotFound(EntityNotFoundException ex) {
        ProblemDetail problem = ProblemDetail.forStatusAndDetail(HttpStatus.NOT_FOUND, ex.getMessage());
        problem.setTitle("Resource Not Found");
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(problem);
    }
}
```

### 3. Data Persistence & Spring Data JPA
- **Entity Lifecycle**: Understanding Transient, Managed, Detached, and Removed persistence states.
- **Query Optimization**: Preventing the $N+1$ select problem via `@EntityGraph`, JOIN FETCH, and projection interfaces.
- **Transactional Integrity**: Declarative `@Transactional(readOnly = true)` for query performance and connection management.
- **Audit Trails**: Automated auditing (`@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`) using Spring Data JPA auditing.

### 4. Security Hardening & Authentication
- **Spring Security 6 Architecture**: Component-based configuration replacing deprecated `WebSecurityConfigurerAdapter`.
- **Stateless JWT Tokens**: HMAC-SHA256 and RSA asymmetric token signing, validation filters, and token refresh rotations.
- **Role-Based Access Control (RBAC)**: Method-level authorization via `@PreAuthorize("hasRole('ADMIN')")`.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http, JwtAuthFilter jwtFilter) throws Exception {
        return http
            .csrf(AbstractHttpConfigurer::disable)
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**", "/actuator/health").permitAll()
                .anyRequest().authenticated()
            )
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class)
            .build();
    }
}
```

### 5. Architectural Paradigms
- **Layered Architecture**: Decoupling Controllers $\to$ Domain Services $\to$ Repositories.
- **Modular Monolith**: Vertical slice architecture partitioning features into self-contained packages prior to microservice decomposition.
- **Observability**: Spring Boot Actuator health checks, Prometheus metrics, and distributed tracing.

---

## 🚀 Reference Implementations

Explore complete, production-grade Spring Boot projects applying these exact patterns:

1. **[Restaurant Management & Kitchen Display Engine](https://github.com/iliass-wakkar/EMSI_Projet_GestionDuRestaurant_SpringBoot)**
   - Spring Boot 3.4.5, Java 17, PostgreSQL, Order Finite State Machine (`CREATED` $\to$ `IN_PREPARATION` $\to$ `SERVED`), transactional table reservation conflict prevention.
2. **[Enterprise Billing & Invoicing Module](https://github.com/iliass-wakkar/Module-de-Facturation-Java-Spring-Boot-)**
   - B2B invoicing engine with SIRET fiscal validation, cascaded line items, dynamic VAT calculation, and comprehensive Postman test suite.
3. **[AriMayi Monolith Backend](https://www.wakkar.net/projects/arimayi-backend-engineering)**
   - High-concurrency monolithic Spring Boot backend engineered with domain-driven vertical slices and OpenAPI contracts.

---

## Recommended Developer Stack

- **JDK**: Java 17 or 21 LTS (Temurin / GraalVM)
- **Framework**: Spring Boot 3.3+ / 3.5+
- **Build Tool**: Apache Maven or Gradle
- **Database**: PostgreSQL with Testcontainers for integration tests
- **API Documentation**: SpringDoc OpenAPI / Swagger UI

---

## Author & Engineering Credits

- **Iliass Wakkar** — Full-Stack & Systems Engineer • [Portfolio](https://www.wakkar.net) • [GitHub](https://github.com/iliass-wakkar) • [LinkedIn](https://www.linkedin.com/in/iliass-wakkar-940233335/)

---

## License

This project is licensed under the Apache License 2.0 — see the [LICENSE](LICENSE) file for details.
