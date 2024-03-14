# Rust GitHub Action

Cross-Platform Composite GitHub Action for Rust Workflows 

## Usage Examples

TODO

## Inputs

|Input  | Description | Required? | Default | Examples | 
|--|--|--|--|--|
| `subcommand` | The subcommand to use with *cargo* or *cross*  | Yes | `check` | `fmt`, `clippy`, `check`, `test`, `doc`, `build`, `run` | 
|`arguments`| The arguments to use with the subcommand | No | ` ` | `--workspace` `--all-features` `--bin binary_name` `-- --help` `-- -V` | 
| `rust_release_channel` | The official Rust release channel to use | No | `stable` | `stable`, `beta`, `nightly` | 
| `use_cross` |Whether to use `cross` instead of using `cargo`| No | `false` | `true`, `false` | 
| `compilation_target` | The Rust [compilation target](https://doc.rust-lang.org/nightly/rustc/platform-support.html) to use  | No | `x86_64-unknown-linux-gnu` | `x86_64-unknown-linux-gnu`, `x86_64-pc-windows-msvc`, `aarch64-apple-darwin` | 

## Steps

Below are the steps that this GitHub action undertakes:

### 1. Checkout Git Repository

Checks out the target git repository in the runner container.
Uses [actions/checkout](https://github.com/actions/checkout)

### 2. Determine Rust Toolchain components

Selects the [Rust toolchain components](https://rust-lang.github.io/rustup/concepts/components.html) to install based on the provided subcommand. If one of the supported subcommands (see examples above) is not provided, [the following components](action.yml#L59) are installed.

### 3. Install Rust Toolchain

Installs the Rust toolchain based on the provided information about the release channel, the compilation target, and the toolchain components. Uses [dtolnay/rust-toolchain](https://github.com/dtolnay/rust-toolchain).

### 4. Install Cross-Compilation Tools

Installs [`cross-rs`](https://github.com/cross-rs/cross). Uses [taiki-e/install-action](https://github.com/taiki-e/install-action).

### 5. Setup Cache

Sets up Rust cache.
Uses [Swatinem/rust-cache](https://github.com/Swatinem/rust-cache)

### 6. Cargo Command

Runs the given subcommand using `cargo` and the provided arguments, if the input `use_cross` is **not** `true`.

### 7. Cross Command

Runs the given subcommand using `cross` and the provided arguments, if the input `use_cross` is `true`.

## Outputs

None. The action fails if any of the steps encounter an error. Otherwise it succeeds.