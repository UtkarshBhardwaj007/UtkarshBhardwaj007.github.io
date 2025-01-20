---
layout: single
title: The Cargo Book
---

## 1. Project Structure
- The default library file is `src/lib.rs`
- The default executable file is `src/main.rs`
- Other executables can be placed in `src/bin/`

## 2. Testing
Cargo looks for tests in two locations:
1. **Source Files (`src/`)**
   - Unit tests
   - Documentation tests

2. **Tests Directory (`tests/`)**
   - Integration tests
   - Requires importing crates

### 2.1 Test Features
- `cargo test` runs:
  - Regular tests
  - Compilation of examples
  - Documentation tests

## 3. CI/CD
- For GitHub Actions CI, workflow is created in `.github/workflows/ci.yml`

## 4. Package Management
### 4.1 Version Control
- **Yanking**: 
  - Marks a version as invalid for new dependencies
  - Existing dependencies continue working
  - Does not delete code

### 4.2 Dependencies
Cargo home:
- Default location: `$HOME/.cargo/`
- Stores downloaded build dependencies

### 4.3 Cargo.toml Configuration
```toml
[dependencies]        # Package library dependencies
[dev-dependencies]   # Dependencies for examples, tests, and benchmarks
```

### 4.4 Package Components
- Targets correspond to source files compilable into crates
- Types of targets:
  - Library
  - Binary
  - Example
  - Test
  - Benchmark

### 4.5 Workspace Features
- Features enabled in `[workspace.dependencies]` apply to all workspace packages
- Dependencies can be marked "optional":
  - Not compiled by default
  - Implicitly defines a feature
  - Included only when feature is enabled
