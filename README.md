# How to indicate WASM support in Rust crates?

When building a WASM-based library or application, it's often hard to tell whether a dependency will work on WebAssembly. Some won't compile. Some won't pass the tests. Some will have runtime errors. Some will behave differently on different `wasm32` targets (such as `-wasi`, `-emscripten`, `-unknown`).

How can crate authors explicitly signal their WASM-readiness? This is what this proposal answers.

## Cargo metadata

In order to signal WebAssembly readiness level, authors can add this section to their `Cargo.toml`:

```toml
[package.metadata.wasm.rs]

# Supported targets (default: `[]`)
targets = ["wasm32-unknown-unknown"]

# This allows to specify fine-grained indication of readiness per target
[package.metadata.wasm.rs.target.'wasm32-unknown-unknown']
# Default: `true` (if the target is listed in `package.metadata.wasm.rs.targets), otherwise `false`)
compiles = true
# Default: `true` (if the target is listed in `package.metadata.wasm.rs.targets), otherwise `false`)
passes-tests = false
# Default: `true` (if the target is listed in `package.metadata.wasm.rs.targets), otherwise `false`)
works = true
# A string that can be used to describe limitations
limited-functionality = "certain functionality is disabled"
```
