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

# Features that WILL NOT work on any WebAssembly target (might not compile, pass tests or
# function correctly)
# ANY `unsupported-features` should be avoided in order for the crate to work properly on all WebAssembly targets (OR condition)
# (default: `[]`)
unsupported-features = ["server"]

# Indicates the minimum WebAssembly version that must be supported by the host in order for this crate to function properly.
# (default: `"1.0"`)
min-wasm-version = "1.0"

# A list of WebAssembly features that must be supported by the host in order for this crate to function properly.
# The names of the features are the names of the respective GitHub repositories that contain the WebAssembly spec proposals. 
# (default: `[]`)
required-wasm-features = ["threads", "simd", "reference-types", "multi-value"]

# The `wasm-readme` field should be the path to a file in the package root (relative to this Cargo.toml) ,
# that contains information about the package's support for WebAssembly, such as limitations and behavioral differences. 
# wasm.rs will interpret it as Markdown and render it on the crate's page.
# (This field is optional)
wasm-readme = "README_WASM.md"

# This allows to specify fine-grained indication of readiness per target
[package.metadata.wasm.rs.target.'wasm32-unknown-unknown']
# A string that can be used to describe limitations
limited-functionality = "certain functionality is disabled"

# Features that are required in order for the crate to work on this target
# Amends, but not overrides `package.metadata.wasm.rs.required-features`
# (default: `[]`)
required-features = ["wasm-unknown"]

# Features that WILL NOT work on this target (might not compile, pass tests or
# function correctly)
# Amends, but not overrides `package.metadata.wasm.rs.unsupported-features`
# (default: `[]`)
unsupported-features = ["wasm-emscripten"]
```
