# Rust GitHub Action

Cross-platform composite GitHub Action for Rust Workflows 

## Usage Examples

Check the [.github/workflows](.github/workflows) directory for usage examples.

## Inputs

|Input  | Description | Required? | Default | Examples | 
|--|--|--|--|--|
| `subcommand` | The subcommand to use with *cargo* or *cross*  | Yes | `check` | `fmt`, `clippy`, `check`, `test`, `doc`, `build`, `run` | 
|`arguments`| The arguments to use with the subcommand | No | ` ` | `--workspace` `--all-features` `--bin binary_name` `-- --help` `-- -V` | 
| `rust_release_channel` | The official Rust release channel to use | No | `stable` | `stable`, `beta`, `nightly` | 
| `use_cross` |Whether to use `cross` instead of using `cargo`| No | `false` | `true`, `false` | 
| `compilation_target` | The Rust [compilation target](https://doc.rust-lang.org/nightly/rustc/platform-support.html) to use  | No | `x86_64-unknown-linux-gnu` | `x86_64-unknown-linux-gnu`, `x86_64-pc-windows-msvc`, `aarch64-apple-darwin` |
| `working_directory` | The directory to run the commands in  | No | `.` | `.`, `./tests/test_app`, `./rust_project_workspace` |

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

### 5. Install llvm-cov

Installs `cargo-llvm-cov`(https://github.com/taiki-e/cargo-llvm-cov). Uses [taiki-e/install-action](https://github.com/taiki-e/install-action).

### 6. Setup Cache

Sets up Rust cache.
Uses [Swatinem/rust-cache](https://github.com/Swatinem/rust-cache)

### 7. Cargo Command

Runs the given subcommand using `cargo` and the provided arguments, if the input `use_cross` is **not** `true`.

### 8. Cross Command

Runs the given subcommand using `cross` and the provided arguments, if the input `use_cross` is `true`.

### 9. Gather Outputs

Collects all possible outputs in a common directory (`target/.dist`).
This is necessary because Cargo creates numerous files and directories at different levels of nesting with no fixed path to the binaries or the documentation across all platforms.

### 10. Upload Artifact

Uploads the files in the form of an artifact and names it `artifact-${{ inputs.subcommand }}-${{ inputs.compilation_target }}`. 
Some examples of possible artifact names: `artifact-build-x86_64-unknown-linux-gnu`, `artifact-doc-x86_64-unknown-linux-gnu`, `artifact-build-x86_64-pc-windows-msvc`, `artifact-build-aarch64-apple-darwin`.

## Outputs

The artifact that contains the built binaries or documentation if relevant. Otherwise none. 
The action fails if any of the steps encounter an error. Otherwise it succeeds.
