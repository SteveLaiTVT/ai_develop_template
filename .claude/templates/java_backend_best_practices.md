# Java Backend Best Practices with OpenAPI & Flyway

This document provides comprehensive best practices for building Java backends with **OpenAPI** specification support and **Flyway** for database migrations.

---

## Overview

| Component | Purpose | Authority |
|-----------|---------|-----------|
| **Spring Boot** | Application framework | Spring Team (VMware) |
| **OpenAPI/Swagger** | API documentation & contract-first design | OpenAPI Initiative |
| **Flyway** | Database version control & migrations | Redgate |
| **Spring Data JPA** | Data access layer | Spring Team |

---

## 1. Project Structure

```
apps/backend/
├── src/
│   ├── main/
│   │   ├── java/
│   │   │   └── com/example/projectname/
│   │   │       ├── ProjectNameApplication.java
│   │   │       ├── config/
│   │   │       │   ├── OpenApiConfig.java
│   │   │       │   ├── SecurityConfig.java
│   │   │       │   └── FlywayConfig.java (optional)
│   │   │       ├── controller/
│   │   │       │   └── v1/
│   │   │       │       ├── AuthController.java
│   │   │       │       └── UserController.java
│   │   │       ├── service/
│   │   │       │   ├── AuthService.java
│   │   │       │   └── UserService.java
│   │   │       ├── repository/
│   │   │       │   └── UserRepository.java
│   │   │       ├── entity/
│   │   │       │   └── User.java
│   │   │       ├── dto/
│   │   │       │   ├── request/
│   │   │       │   │   ├── RegisterRequest.java
│   │   │       │   │   └── LoginRequest.java
│   │   │       │   └── response/
│   │   │       │       ├── ApiResponse.java
│   │   │       │       └── UserResponse.java
│   │   │       ├── exception/
│   │   │       │   ├── GlobalExceptionHandler.java
│   │   │       │   └── BusinessException.java
│   │   │       └── mapper/
│   │   │           └── UserMapper.java
│   │   └── resources/
│   │       ├── application.yml
│   │       ├── application-dev.yml
│   │       ├── application-prod.yml
│   │       └── db/
│   │           └── migration/
│   │               ├── V1__create_users_table.sql
│   │               ├── V2__add_user_roles.sql
│   │               └── V3__add_user_profile.sql
│   └── test/
│       └── java/
│           └── com/example/projectname/
│               ├── controller/
│               ├── service/
│               └── repository/
├── pom.xml (or build.gradle)
└── README.md
```

---

## 2. OpenAPI Configuration

### 2.1 Dependencies (Maven)

```xml
<dependencies>
    <!-- Spring Boot Starter Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- OpenAPI / Springdoc -->
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
        <version>2.3.0</version>
    </dependency>

    <!-- Validation -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
</dependencies>
```

### 2.2 Dependencies (Gradle)

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.3.0'
    implementation 'org.springframework.boot:spring-boot-starter-validation'
}
```

### 2.3 OpenAPI Configuration Class

```java
package com.example.projectname.config;

import io.swagger.v3.oas.models.OpenAPI;
import io.swagger.v3.oas.models.info.Contact;
import io.swagger.v3.oas.models.info.Info;
import io.swagger.v3.oas.models.info.License;
import io.swagger.v3.oas.models.security.SecurityRequirement;
import io.swagger.v3.oas.models.security.SecurityScheme;
import io.swagger.v3.oas.models.Components;
import io.swagger.v3.oas.models.servers.Server;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.List;

@Configuration
public class OpenApiConfig {

    @Value("${spring.application.name:API}")
    private String applicationName;

    @Value("${server.port:8080}")
    private String serverPort;

    @Bean
    public OpenAPI customOpenAPI() {
        final String securitySchemeName = "bearerAuth";

        return new OpenAPI()
            .info(new Info()
                .title(applicationName + " API")
                .version("1.0.0")
                .description("REST API documentation for " + applicationName)
                .contact(new Contact()
                    .name("Development Team")
                    .email("dev@example.com"))
                .license(new License()
                    .name("MIT License")
                    .url("https://opensource.org/licenses/MIT")))
            .servers(List.of(
                new Server()
                    .url("http://localhost:" + serverPort)
                    .description("Development Server"),
                new Server()
                    .url("https://api.example.com")
                    .description("Production Server")))
            .addSecurityItem(new SecurityRequirement()
                .addList(securitySchemeName))
            .components(new Components()
                .addSecuritySchemes(securitySchemeName,
                    new SecurityScheme()
                        .name(securitySchemeName)
                        .type(SecurityScheme.Type.HTTP)
                        .scheme("bearer")
                        .bearerFormat("JWT")
                        .description("Enter JWT token")));
    }
}
```

### 2.4 Application Properties for OpenAPI

```yaml
# application.yml
springdoc:
  api-docs:
    enabled: true
    path: /api-docs
  swagger-ui:
    enabled: true
    path: /swagger-ui.html
    operations-sorter: method
    tags-sorter: alpha
    try-it-out-enabled: true
    filter: true
  show-actuator: false
  default-produces-media-type: application/json
  default-consumes-media-type: application/json
```

### 2.5 Controller with OpenAPI Annotations

```java
package com.example.projectname.controller.v1;

import com.example.projectname.dto.request.RegisterRequest;
import com.example.projectname.dto.request.LoginRequest;
import com.example.projectname.dto.response.ApiResponse;
import com.example.projectname.dto.response.AuthResponse;
import com.example.projectname.service.AuthService;
import io.swagger.v3.oas.annotations.Operation;
import io.swagger.v3.oas.annotations.Parameter;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import io.swagger.v3.oas.annotations.tags.Tag;
import io.swagger.v3.oas.annotations.security.SecurityRequirement;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/v1/auth")
@RequiredArgsConstructor
@Tag(name = "Authentication", description = "User authentication and registration APIs")
public class AuthController {

    private final AuthService authService;

    @Operation(
        summary = "Register a new user",
        description = "Creates a new user account with email and password"
    )
    @ApiResponses(value = {
        @io.swagger.v3.oas.annotations.responses.ApiResponse(
            responseCode = "201",
            description = "User registered successfully",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ApiResponse.class)
            )
        ),
        @io.swagger.v3.oas.annotations.responses.ApiResponse(
            responseCode = "400",
            description = "Invalid input data",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ApiResponse.class)
            )
        ),
        @io.swagger.v3.oas.annotations.responses.ApiResponse(
            responseCode = "409",
            description = "Email already exists",
            content = @Content(
                mediaType = "application/json",
                schema = @Schema(implementation = ApiResponse.class)
            )
        )
    })
    @PostMapping("/register")
    public ResponseEntity<ApiResponse<AuthResponse>> register(
            @Valid @RequestBody RegisterRequest request) {
        AuthResponse response = authService.register(request);
        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(ApiResponse.success(response, "User registered successfully"));
    }

    @Operation(
        summary = "User login",
        description = "Authenticates user and returns JWT token"
    )
    @PostMapping("/login")
    public ResponseEntity<ApiResponse<AuthResponse>> login(
            @Valid @RequestBody LoginRequest request) {
        AuthResponse response = authService.login(request);
        return ResponseEntity.ok(ApiResponse.success(response, "Login successful"));
    }

    @Operation(
        summary = "Get current user profile",
        description = "Returns the profile of the currently authenticated user",
        security = @SecurityRequirement(name = "bearerAuth")
    )
    @GetMapping("/me")
    public ResponseEntity<ApiResponse<UserResponse>> getCurrentUser(
            @Parameter(hidden = true) @AuthenticationPrincipal UserDetails userDetails) {
        // TODO(B): Implement get current user
        throw new UnsupportedOperationException("Not implemented");
    }
}
```

### 2.6 DTO with OpenAPI Schema

```java
package com.example.projectname.dto.request;

import io.swagger.v3.oas.annotations.media.Schema;
import jakarta.validation.constraints.*;
import lombok.Data;

@Data
@Schema(description = "User registration request")
public class RegisterRequest {

    @Schema(
        description = "User's email address",
        example = "user@example.com",
        requiredMode = Schema.RequiredMode.REQUIRED
    )
    @NotBlank(message = "Email is required")
    @Email(message = "Invalid email format")
    @Size(max = 255, message = "Email must not exceed 255 characters")
    private String email;

    @Schema(
        description = "User's password (min 8 characters)",
        example = "SecurePass123!",
        requiredMode = Schema.RequiredMode.REQUIRED,
        minLength = 8,
        maxLength = 100
    )
    @NotBlank(message = "Password is required")
    @Size(min = 8, max = 100, message = "Password must be 8-100 characters")
    @Pattern(
        regexp = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d).*$",
        message = "Password must contain uppercase, lowercase, and number"
    )
    private String password;

    @Schema(
        description = "User's display name",
        example = "John Doe",
        requiredMode = Schema.RequiredMode.REQUIRED
    )
    @NotBlank(message = "Name is required")
    @Size(min = 2, max = 100, message = "Name must be 2-100 characters")
    private String name;
}
```

### 2.7 Standard API Response

```java
package com.example.projectname.dto.response;

import com.fasterxml.jackson.annotation.JsonInclude;
import io.swagger.v3.oas.annotations.media.Schema;
import lombok.Builder;
import lombok.Data;

@Data
@Builder
@JsonInclude(JsonInclude.Include.NON_NULL)
@Schema(description = "Standard API response wrapper")
public class ApiResponse<T> {

    @Schema(description = "Indicates if the request was successful")
    private boolean success;

    @Schema(description = "Response message")
    private String message;

    @Schema(description = "Response data")
    private T data;

    @Schema(description = "Error details (only present on failure)")
    private ErrorDetails error;

    @Schema(description = "Pagination metadata (only for list responses)")
    private PageMeta meta;

    public static <T> ApiResponse<T> success(T data) {
        return ApiResponse.<T>builder()
            .success(true)
            .data(data)
            .build();
    }

    public static <T> ApiResponse<T> success(T data, String message) {
        return ApiResponse.<T>builder()
            .success(true)
            .message(message)
            .data(data)
            .build();
    }

    public static <T> ApiResponse<T> error(String code, String message) {
        return ApiResponse.<T>builder()
            .success(false)
            .error(ErrorDetails.builder()
                .code(code)
                .message(message)
                .build())
            .build();
    }

    @Data
    @Builder
    @Schema(description = "Error details")
    public static class ErrorDetails {
        @Schema(description = "Error code", example = "AUTH_001")
        private String code;

        @Schema(description = "Error message", example = "Invalid credentials")
        private String message;

        @Schema(description = "Additional error details")
        private Object details;
    }

    @Data
    @Builder
    @Schema(description = "Pagination metadata")
    public static class PageMeta {
        @Schema(description = "Current page number", example = "1")
        private int page;

        @Schema(description = "Items per page", example = "20")
        private int size;

        @Schema(description = "Total number of items", example = "100")
        private long total;

        @Schema(description = "Total number of pages", example = "5")
        private int totalPages;
    }
}
```

---

## 3. Flyway Database Migrations

### 3.1 Dependencies (Maven)

```xml
<dependencies>
    <!-- Flyway -->
    <dependency>
        <groupId>org.flywaydb</groupId>
        <artifactId>flyway-core</artifactId>
    </dependency>

    <!-- For MySQL -->
    <dependency>
        <groupId>org.flywaydb</groupId>
        <artifactId>flyway-mysql</artifactId>
    </dependency>

    <!-- For PostgreSQL (no extra dependency needed) -->

    <!-- Database Driver -->
    <dependency>
        <groupId>org.postgresql</groupId>
        <artifactId>postgresql</artifactId>
        <scope>runtime</scope>
    </dependency>

    <!-- Spring Data JPA -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
```

### 3.2 Dependencies (Gradle)

```groovy
dependencies {
    implementation 'org.flywaydb:flyway-core'
    // For MySQL
    implementation 'org.flywaydb:flyway-mysql'

    runtimeOnly 'org.postgresql:postgresql'
    implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}
```

### 3.3 Flyway Configuration

```yaml
# application.yml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/mydb
    username: ${DB_USERNAME:postgres}
    password: ${DB_PASSWORD:postgres}
    driver-class-name: org.postgresql.Driver

  jpa:
    hibernate:
      ddl-auto: validate  # IMPORTANT: Use 'validate' with Flyway, never 'create' or 'update'
    show-sql: false
    properties:
      hibernate:
        format_sql: true
        dialect: org.hibernate.dialect.PostgreSQLDialect

  flyway:
    enabled: true
    baseline-on-migrate: true
    baseline-version: 0
    locations: classpath:db/migration
    validate-on-migrate: true
    out-of-order: false
    table: flyway_schema_history
    clean-disabled: true  # NEVER allow clean in production
```

### 3.4 Migration Naming Convention

```
V{version}__{description}.sql

Examples:
V1__create_users_table.sql
V2__add_user_roles_table.sql
V3__add_email_index_to_users.sql
V1.1__add_phone_to_users.sql
V1.2__create_audit_columns.sql
```

**Naming Rules:**
- `V` prefix for versioned migrations (run once)
- `R` prefix for repeatable migrations (run every time content changes)
- Double underscore `__` separates version from description
- Use underscores in description (no spaces)
- Versions must be unique and sequential

### 3.5 Migration Examples

#### V1__create_users_table.sql

```sql
-- V1__create_users_table.sql
-- Create users table with basic fields

CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    email VARCHAR(255) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    name VARCHAR(100) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

-- Create index on email for login queries
CREATE INDEX idx_users_email ON users(email);

-- Create index on status for filtering
CREATE INDEX idx_users_status ON users(status);

COMMENT ON TABLE users IS 'User accounts';
COMMENT ON COLUMN users.status IS 'ACTIVE, INACTIVE, SUSPENDED, DELETED';
```

#### V2__add_user_roles.sql

```sql
-- V2__add_user_roles.sql
-- Add roles table and user-role relationship

CREATE TABLE roles (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(50) NOT NULL UNIQUE,
    description VARCHAR(255),
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE user_roles (
    user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    role_id BIGINT NOT NULL REFERENCES roles(id) ON DELETE CASCADE,
    assigned_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id, role_id)
);

-- Insert default roles
INSERT INTO roles (name, description) VALUES
    ('ROLE_USER', 'Standard user role'),
    ('ROLE_ADMIN', 'Administrator role');

-- Create index for role lookups
CREATE INDEX idx_user_roles_user_id ON user_roles(user_id);
CREATE INDEX idx_user_roles_role_id ON user_roles(role_id);
```

#### V3__add_refresh_tokens.sql

```sql
-- V3__add_refresh_tokens.sql
-- Add refresh token storage for JWT authentication

CREATE TABLE refresh_tokens (
    id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL REFERENCES users(id) ON DELETE CASCADE,
    token VARCHAR(255) NOT NULL UNIQUE,
    expires_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    revoked_at TIMESTAMP
);

-- Index for token lookup
CREATE INDEX idx_refresh_tokens_token ON refresh_tokens(token);

-- Index for cleanup of expired tokens
CREATE INDEX idx_refresh_tokens_expires_at ON refresh_tokens(expires_at);

-- Index for user's tokens
CREATE INDEX idx_refresh_tokens_user_id ON refresh_tokens(user_id);
```

#### V4__add_audit_columns.sql

```sql
-- V4__add_audit_columns.sql
-- Add audit columns to existing tables

ALTER TABLE users
    ADD COLUMN created_by BIGINT,
    ADD COLUMN updated_by BIGINT;

-- Add trigger for updated_at
CREATE OR REPLACE FUNCTION update_updated_at_column()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$ language 'plpgsql';

CREATE TRIGGER update_users_updated_at
    BEFORE UPDATE ON users
    FOR EACH ROW
    EXECUTE FUNCTION update_updated_at_column();
```

#### R__seed_test_data.sql (Repeatable Migration)

```sql
-- R__seed_test_data.sql
-- Repeatable migration for test data (dev environment only)
-- This runs every time the file content changes

-- Only run in development
DO $$
BEGIN
    IF current_setting('app.environment', true) = 'development' THEN
        -- Clear existing test data
        DELETE FROM users WHERE email LIKE '%@test.example.com';

        -- Insert test users
        INSERT INTO users (email, password_hash, name, status)
        VALUES
            ('admin@test.example.com', '$2a$12$test...', 'Test Admin', 'ACTIVE'),
            ('user@test.example.com', '$2a$12$test...', 'Test User', 'ACTIVE');
    END IF;
END $$;
```

### 3.6 Entity Mapping

```java
package com.example.projectname.entity;

import jakarta.persistence.*;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.AllArgsConstructor;
import lombok.Builder;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.LocalDateTime;
import java.util.Set;

@Entity
@Table(name = "users")
@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, unique = true, length = 255)
    private String email;

    @Column(name = "password_hash", nullable = false, length = 255)
    private String passwordHash;

    @Column(nullable = false, length = 100)
    private String name;

    @Enumerated(EnumType.STRING)
    @Column(nullable = false, length = 20)
    private UserStatus status = UserStatus.ACTIVE;

    @ManyToMany(fetch = FetchType.LAZY)
    @JoinTable(
        name = "user_roles",
        joinColumns = @JoinColumn(name = "user_id"),
        inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles;

    @CreationTimestamp
    @Column(name = "created_at", nullable = false, updatable = false)
    private LocalDateTime createdAt;

    @UpdateTimestamp
    @Column(name = "updated_at", nullable = false)
    private LocalDateTime updatedAt;

    @Column(name = "created_by")
    private Long createdBy;

    @Column(name = "updated_by")
    private Long updatedBy;

    public enum UserStatus {
        ACTIVE, INACTIVE, SUSPENDED, DELETED
    }
}
```

### 3.7 Flyway Best Practices

| Practice | Description |
|----------|-------------|
| **Never modify deployed migrations** | Once a migration runs in any environment, it's immutable |
| **Use `validate` for JPA ddl-auto** | Let Flyway manage schema, JPA only validates |
| **Version numbers are sequential** | No gaps, no duplicates |
| **One concern per migration** | Keep migrations small and focused |
| **Always provide rollback (manual)** | Document how to rollback each migration |
| **Test migrations in staging first** | Never apply untested migrations to production |
| **Use transactions** | Migrations are wrapped in transactions by default |
| **Clean is for dev only** | Set `clean-disabled: true` in production |

### 3.8 Migration Rollback Documentation

Create a companion file for complex migrations:

```sql
-- V4__add_audit_columns.sql.rollback
-- MANUAL ROLLBACK - DO NOT RUN AUTOMATICALLY

-- Remove trigger
DROP TRIGGER IF EXISTS update_users_updated_at ON users;
DROP FUNCTION IF EXISTS update_updated_at_column();

-- Remove columns
ALTER TABLE users
    DROP COLUMN IF EXISTS created_by,
    DROP COLUMN IF EXISTS updated_by;
```

---

## 4. Complete pom.xml Example

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>projectname</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Project Name</name>
    <description>Spring Boot backend with OpenAPI and Flyway</description>

    <properties>
        <java.version>21</java.version>
        <springdoc.version>2.3.0</springdoc.version>
    </properties>

    <dependencies>
        <!-- Spring Boot Starters -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <!-- OpenAPI / Springdoc -->
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
            <version>${springdoc.version}</version>
        </dependency>

        <!-- Flyway -->
        <dependency>
            <groupId>org.flywaydb</groupId>
            <artifactId>flyway-core</artifactId>
        </dependency>

        <!-- Database -->
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>

        <!-- JWT -->
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-api</artifactId>
            <version>0.12.3</version>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-impl</artifactId>
            <version>0.12.3</version>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>io.jsonwebtoken</groupId>
            <artifactId>jjwt-jackson</artifactId>
            <version>0.12.3</version>
            <scope>runtime</scope>
        </dependency>

        <!-- Lombok -->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>

        <!-- MapStruct for DTO mapping -->
        <dependency>
            <groupId>org.mapstruct</groupId>
            <artifactId>mapstruct</artifactId>
            <version>1.5.5.Final</version>
        </dependency>

        <!-- Test Dependencies -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <annotationProcessorPaths>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </path>
                        <path>
                            <groupId>org.mapstruct</groupId>
                            <artifactId>mapstruct-processor</artifactId>
                            <version>1.5.5.Final</version>
                        </path>
                        <path>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok-mapstruct-binding</artifactId>
                            <version>0.2.0</version>
                        </path>
                    </annotationProcessorPaths>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

---

## 5. Exception Handling with OpenAPI

```java
package com.example.projectname.exception;

import com.example.projectname.dto.response.ApiResponse;
import io.swagger.v3.oas.annotations.media.Content;
import io.swagger.v3.oas.annotations.media.Schema;
import io.swagger.v3.oas.annotations.responses.ApiResponses;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseStatus;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.util.HashMap;
import java.util.Map;

@Slf4j
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ApiResponse<Void> handleValidationException(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        ex.getBindingResult().getAllErrors().forEach(error -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });

        return ApiResponse.<Void>builder()
            .success(false)
            .error(ApiResponse.ErrorDetails.builder()
                .code("VALIDATION_ERROR")
                .message("Validation failed")
                .details(errors)
                .build())
            .build();
    }

    @ExceptionHandler(BusinessException.class)
    public ResponseEntity<ApiResponse<Void>> handleBusinessException(BusinessException ex) {
        log.warn("Business exception: {}", ex.getMessage());

        return ResponseEntity
            .status(ex.getStatus())
            .body(ApiResponse.error(ex.getCode(), ex.getMessage()));
    }

    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ApiResponse<Void> handleGenericException(Exception ex) {
        log.error("Unexpected error", ex);

        return ApiResponse.error("INTERNAL_ERROR", "An unexpected error occurred");
    }
}
```

```java
package com.example.projectname.exception;

import lombok.Getter;
import org.springframework.http.HttpStatus;

@Getter
public class BusinessException extends RuntimeException {
    private final String code;
    private final HttpStatus status;

    public BusinessException(String code, String message, HttpStatus status) {
        super(message);
        this.code = code;
        this.status = status;
    }

    public static BusinessException notFound(String resource) {
        return new BusinessException(
            "NOT_FOUND",
            resource + " not found",
            HttpStatus.NOT_FOUND
        );
    }

    public static BusinessException conflict(String message) {
        return new BusinessException(
            "CONFLICT",
            message,
            HttpStatus.CONFLICT
        );
    }

    public static BusinessException unauthorized(String message) {
        return new BusinessException(
            "UNAUTHORIZED",
            message,
            HttpStatus.UNAUTHORIZED
        );
    }
}
```

---

## 6. OpenAPI Access URLs

After starting the application, access:

| Resource | URL |
|----------|-----|
| Swagger UI | http://localhost:8080/swagger-ui.html |
| OpenAPI JSON | http://localhost:8080/api-docs |
| OpenAPI YAML | http://localhost:8080/api-docs.yaml |

---

## 7. Best Practices Summary

### OpenAPI Best Practices

| Practice | Description |
|----------|-------------|
| **Use @Tag for grouping** | Group related endpoints under tags |
| **Document all responses** | Include success and error response schemas |
| **Use @Schema for models** | Provide examples and descriptions |
| **Version your API** | Use `/api/v1/` prefix in URL |
| **Security annotations** | Mark protected endpoints with @SecurityRequirement |
| **Validate inputs** | Use Bean Validation with OpenAPI schemas |

### Flyway Best Practices

| Practice | Description |
|----------|-------------|
| **Sequential versioning** | V1, V2, V3... no gaps |
| **Descriptive names** | `V1__create_users_table.sql` |
| **Small migrations** | One concern per migration |
| **Never edit deployed** | Migrations are immutable once run |
| **Use validate mode** | JPA ddl-auto=validate |
| **Disable clean** | `clean-disabled: true` in production |
| **Test in staging** | Always test migrations before production |

### Integration Best Practices

| Practice | Description |
|----------|-------------|
| **DTO validation** | Use Bean Validation + OpenAPI schemas |
| **Consistent responses** | Use standard ApiResponse wrapper |
| **Error handling** | Global exception handler with proper codes |
| **Database-first** | Flyway manages schema, JPA validates |
| **Environment configs** | Profile-specific application-{env}.yml |

---

## 8. Spring Initializr Quick Start

Use [Spring Initializr](https://start.spring.io) with these settings:

```
Project: Maven
Language: Java
Spring Boot: 3.2.x
Packaging: Jar
Java: 21

Dependencies:
- Spring Web
- Spring Data JPA
- Spring Security
- Validation
- PostgreSQL Driver
- Flyway Migration
- Lombok
- Spring Boot Actuator
```

Then add springdoc-openapi manually to pom.xml.

---

## References

- [Springdoc OpenAPI Documentation](https://springdoc.org/)
- [OpenAPI Specification](https://spec.openapis.org/oas/v3.1.0)
- [Flyway Documentation](https://documentation.red-gate.com/flyway)
- [Spring Boot Reference](https://docs.spring.io/spring-boot/docs/current/reference/html/)
