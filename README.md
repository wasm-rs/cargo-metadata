# How to indicate WASM support in Rust crates?

When building a WASM-based library or application, it's often hard to tell whether a dependency will work on WebAssembly. Some won't compile. Some won't pass the tests. Some will have runtime errors. Some will behave differently on different `wasm32` targets (such as `-wasi`, `-emscripten`, `-unknown`).

How can crate authors explicitly signal their WASM-readiness? This is what this proposal answers.

## Cargo metadata

In order to signal WebAssembly readiness level, authors can add this section to their `Cargo.toml`:

```toml
[package.metadata.wasm.rs]

# Supported targets (default: `[]`)
targets = ["wasm32-unknown-unknown"]

# Features that are required in order for the crate to work on any WebAssembly target
# ALL `required-features` should be specified in order for the crate to work on all WebAssembly targets (AND condition)
# (default: `[]`)
required-features = ["wasm"]

# Features that WILL NOT work on any WebAssembly target (might not compile, pass test or
# function correctly)
# ANY `excluded-features` should be avoided in order for the crate to work properly on all WebAssembly targets (OR condition)
# (default: `[]`)
excluded-features = ["server"]

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

# Features that are required in order for the crate to work on this target
# Amends, but not overrides `package.metadata.wasm.rs.required-features`
# (default: `[]`)
required-features = ["wasm-unknown"]

# Features that WILL NOT work on this target (might not compile, pass test or
# function correctly)
# Amends, but not overrides `package.metadata.wasm.rs.excluded-features`
# (default: `[]`)
excluded-features = ["wasm-emscripten"]
```
