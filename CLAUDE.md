# CLAUDE.md

Kotlin/Spring Boot service following strict hexagonal architecture.
Read this before writing any code.

---

## Project Structure

```
src/main/kotlin/{base.package}/
├── domain/
│   ├── {aggregate}/model|error|event/
│   └── shared/model/
├── usecase/
│   └── {aggregate}/ports/inbound|outbound + service/
├── adapters/
│   ├── inbound/web/{aggregate}/ + dto/
│   └── outbound/persistence/{aggregate}/ + messaging/
└── config/
```

---

## Architecture Rules (ArchUnit enforces — fix the code, not the test)

- `domain/` and `usecase/` have zero Spring, Jakarta, or infrastructure imports
- `adapters/inbound/` never imports from `domain/` — only through `usecase/ports/`
- `adapters/outbound/` may use domain model/events for mapping, never calls domain services
- All classes in `usecase/ports/` must be interfaces
- `usecase/` never imports from `adapters/`
- No package cycles between top-level packages

---

## Kotlin Conventions

**Error handling:** `com.github.michaelbull.result` only (`Ok`, `Err`, `andThen`, `mapError`, `fold`). Never throw from domain methods — return `Err(DomainError...)`.

**Aggregates:** private constructor, `companion object { fun create(...): Result<T, E> }` factory, `fun rehydrate(...)` for persistence reconstruction, `pullEvents()` for domain events.

**IDs:** `@JvmInline value class FooId(val value: UUID)` with `generate()` and `from(String)` companions.

**Domain errors:** `sealed class` with `data object` / `data class` cases — never extend `Exception`.

**Logging:** `io.github.oshai.kotlinlogging.KotlinLogging` — `private val log = KotlinLogging.logger {}`

**Language:** all code, comments, and OpenAPI descriptions in English.

---

## Tech Stack

| Concern | Choice |
|---|---|
| Language | Kotlin, Java 21 |
| Framework | Spring Boot 3.x |
| Database | MongoDB |
| Messaging | Apache Pulsar |
| Auth | Keycloak OAuth2/JWT |
| API docs | SpringDoc OpenAPI |
| Tests | JUnit 5 + Testcontainers (integration) |
| Build | Maven (`./mvnw`) |

---

## API Conventions

- Base path: `/api/v1/`
- Response wrapper: `{ data, status, message?, errors? }`
- Pagination: `?page=0&size=20` (default 20, max 100), dates in ISO 8601 UTC
- HTTP codes: `201` create · `200` read/update · `204` delete · `409` optimistic lock conflict
- All controller errors go through a global exception handler via a dedicated exception type
- Security enforced at controller level via `@PreAuthorize`
- JWT claims extracted per request (user id, company id, roles)

---

## Commands

```bash
./mvnw clean compile -q          # first check after any change
./mvnw test                      # unit tests + ArchUnit
./mvnw verify                    # + integration tests (requires Docker)
./mvnw spring-boot:run           # run locally (docker-compose up -d first)
```

---

## Do Nots

- Never add Spring/Jakarta/infrastructure imports inside `domain/` or `usecase/`
- Never modify ArchUnit rules to fix a build — fix the code instead
- Never call domain services from outbound adapters
- Never bypass the global exception handler in controllers
- Never import adapter classes from `usecase/` or `domain/`
- Never skip `@Operation` / `@ApiResponse` on controller endpoints
- Never log sensitive data — log format is JSON
