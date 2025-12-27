# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

---

## Basic Policies

### Language Settings

- **Communication with developers**: Japanese
- **Source code**: English (variable names, function names, comments)
- **Commit messages**: English
- **Documentation**: Create and update both Japanese and English versions

### Multilingual Documentation

**Important**: When creating or updating permanent documentation such as ADRs (Architecture Decision Records) or architecture documents, always provide both Japanese and English versions.

#### Root-level Files (README, CONTRIBUTING, etc.)

For root-level markdown files, use English as the primary version with Japanese as `*.ja.md`:

```
soboro/
├── README.md                # English version (primary)
├── README.ja.md             # Japanese version
├── CONTRIBUTING.md          # English version (primary)
└── CONTRIBUTING.ja.md       # Japanese version
```

Each file must include a language switcher link at the top:
- English version: `> 🇯🇵 [日本語版はこちら](README.ja.md)`
- Japanese version: `> 🇺🇸 [English version](README.md)`

#### Documentation Directory

```
docs/
├── architecture/
│   ├── ja/overview.md      # Japanese version
│   └── en/overview.md      # English version (required)
├── guides/
│   ├── ja/
│   └── en/
└── decisions/
    ├── ja/ADR-0001-*.md    # Japanese version
    └── en/ADR-0001-*.md    # English version (required)
```

Documentation update checklist:
- [ ] Created/updated Japanese version
- [ ] Created/updated English version
- [ ] Content is consistent across both languages
- [ ] Language switcher links are present and correct

### Keeping Documentation Up-to-Date

- When there are updates to project rules, structure, or conventions, update README.md and/or CLAUDE.md as needed

---

## Project Structure

### Directory Structure and Responsibilities

| Directory | Responsibility | Development Notes |
|-----------|---------------|-------------------|
| `bindings/` | Thin wrappers for C libraries | Swift-idiomatic APIs go in `libs/`. Keep this minimal FFI only |
| `libs/` | Reusable Swift libraries | Emphasize protocol-oriented design. Minimize dependencies |
| `core/` | Core components required at boot | Minimal dependencies. Prioritize performance |
| `services/` | System daemons | Single responsibility. Include D-Bus interface definitions |
| `desktop/` | GUI environment | Prioritize performance. Leverage async/await |
| `apps/` | End-user applications | Use SoboroUI. Consider sandbox compatibility |

### Dependency Flow

```
bindings/ (C FFI)
    ↓
libs/ (Swift abstraction layer)
    ↓
core/ / services/ / desktop/ / apps/ (implementations)
```

Dependencies from lower layers to upper layers are prohibited.

---

## Code Generation Guidelines

### Naming Conventions

See README.md "Naming Conventions" section for detailed naming rules.

### Error Handling

Leverage Swift 6 Typed Throws:

```swift
// Recommended: Specify concrete error types
func loadConfig() throws(ConfigError) -> Config {
    // ...
}

// System-level errors
enum SoboroError: Error {
    case fileNotFound(path: String)
    case permissionDenied
    case timeout(duration: Duration)
}
```

### Concurrency

Leverage Swift 6 Actor model:

```swift
// Implement services as Actors
actor NetworkService {
    private var connections: [Connection] = []

    func connect(to host: String) async throws {
        // ...
    }
}

// MainActor for UI components only
@MainActor
final class WindowController {
    // ...
}
```

### Documentation

Always add DocC comments to public APIs:

```swift
/// Manages the lifecycle of services
///
/// `ServiceManager` resolves service dependencies at system startup
/// and starts/stops them in the appropriate order.
///
/// ## Example
///
/// ```swift
/// let manager = ServiceManager()
/// try await manager.startAll()
/// ```
///
/// - Note: This class is used by `soboro-init`
public actor ServiceManager {
    /// Starts all services
    ///
    /// - Throws: `ServiceError.dependencyFailed` if a dependent service fails to start
    public func startAll() async throws(ServiceError) {
        // ...
    }
}
```

---

## Prompt Templates

### Creating a New Component

```
Please implement soboro-{component-name}.

Requirements:
- {requirement 1}
- {requirement 2}

References:
- Existing component: {similar existing code}
- External specification: {specifications or documents to reference}

Follow the style in libs/SoboroFoundation/.
```

### Bug Fixes

```
There is an issue with {component}.

Symptom: {description of the problem}

Steps to reproduce:
1. {step 1}
2. {step 2}

Expected behavior: {expected behavior}
Actual behavior: {actual behavior}

Related files: {file paths}
```

### Refactoring

```
Please refactor {target file/module}.

Purpose:
- {purpose of refactoring}

Constraints:
- Do not change public APIs
- All existing tests must pass
- {other constraints}
```

### Documentation Creation

```
Please create documentation for {feature/component}.

Location: docs/{category}/

Content:
- {content to include}

Note: Create both Japanese (ja/) and English (en/) versions.
```

### Creating an ADR (Architecture Decision Record)

```
Please create an ADR for the following design decision.

Title: {decision}
Context: {background and issues}
Options:
- {option A}
- {option B}
Decision: {chosen option and rationale}

Location: docs/decisions/
Note: Create in both ja/ and en/.
```

---

## Testing Requirements

### Unit Tests

Use Swift Testing framework:

```swift
import Testing
@testable import SoboroFoundation

@Suite("ServiceManager Tests")
struct ServiceManagerTests {
    @Test("Services start in correct order")
    func servicesStartInOrder() async throws {
        let manager = ServiceManager()
        // ...
    }

    @Test("Detects circular dependencies", .tags(.validation))
    func detectCircularDependency() async throws {
        // ...
    }
}
```

### Test Coverage

- New features: Target 80% or higher coverage
- Bug fixes: Add regression tests for the fixed area

---

## Git Workflow

### Before Starting Work

- Always confirm with the user whether to create a new branch before starting work

### Commits

- Make commits in the smallest possible units
- Prefer line-level staging over file-level staging when appropriate
- Always confirm with the user before committing after each task
- Only commit after receiving explicit approval from the user

---

## Pre-Commit Checklist

After generating or modifying code, verify the following:

- [ ] `swift build` succeeds
- [ ] `swift test` passes all tests
- [ ] New public APIs have DocC documentation
- [ ] Commit message follows conventions (English, conventional commits)
- [ ] When updating documentation, both Japanese and English versions are updated
- [ ] For major design changes, an ADR has been created

---

## Common Commands

```bash
# Build
swift build

# Test
swift test

# Run specific tests only
swift test --filter ServiceManagerTests

# Generate documentation
swift package generate-documentation

# Format
swift format --in-place --recursive Sources/

# Lint (to be added in the future)
# swift lint Sources/
```

---

## Prohibited Practices

1. **No force unwrap (`!`)**
   - Use `guard let` or `if let`

2. **No implicit global state**
   - Use dependency injection

3. **No synchronous blocking I/O**
   - Use async/await

4. **No unauthorized external dependencies**
   - Create an ADR when adding new external packages

---

## Reference Information

For project goals, technology stack, and architecture overview, see README.md.

### Related Documentation

- `README.md` - Project overview (English)
- `README.ja.md` - Project overview (Japanese)
- `CONTRIBUTING.md` - Contribution guide
- `docs/architecture/` - Architecture design
- `docs/decisions/` - ADRs (Architecture Decision Records)
