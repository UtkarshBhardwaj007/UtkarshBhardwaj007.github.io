# The Cargo Book

## Project Structure
- The default library file is `src/lib.rs`
- The default executable file is `src/main.rs`
- Other executables can be placed in `src/bin/`

## Testing
Cargo looks for tests in two locations:
1. **Source Files (`src/`)**
   - Unit tests
   - Documentation tests

2. **Tests Directory (`tests/`)**
   - Integration tests
   - Requires importing crates

### Test Features
- `cargo test` runs:
  - Regular tests
  - Compilation of examples
  - Documentation tests

## CI/CD
- For GitHub Actions CI, workflow is created in `.github/workflows/ci.yml`

## Package Management
### Version Control
- **Yanking**: 
  - Marks a version as invalid for new dependencies
  - Existing dependencies continue working
  - Does not delete code

### Dependencies
Cargo home:
- Default location: `$HOME/.cargo/`
- Stores downloaded build dependencies

### Cargo.toml Configuration
```toml
[dependencies]        # Package library dependencies
[dev-dependencies]   # Dependencies for examples, tests, and benchmarks
```

### Package Components
- Targets correspond to source files compilable into crates
- Types of targets:
  - Library
  - Binary
  - Example
  - Test
  - Benchmark

### Workspace Features
- Features enabled in `[workspace.dependencies]` apply to all workspace packages
- Dependencies can be marked "optional":
  - Not compiled by default
  - Implicitly defines a feature
  - Included only when feature is enabled
