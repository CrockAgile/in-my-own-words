# rustbuild - Bootstrapping Rust

## Build

`x.py build` accepts arguments:

- `--stage N` - build the stage N compiler
- `src/libstd` - build libstd
- `src/libtest` - build specific crate
- `--keep-stage N` - skip building stage N even if files are dirty
- `test` - run all tests
- `test src/test/run-pass` - run specific test suite
- `test --test-args substring-of-test-name` - run tests matching substring
- `doc` - build documentation

## Configuration

Configure via `config.toml`

## Build Stages

1. `x.py` begins build
1. Download stage 0 compiler & cargo binaries
1. Compile `rustbuild` (this project)
1. Sanity check available compilers, etc.
1. Build stage 0 artifacts
1. Build stdlib and compiler w/ stage 0 cargo
1. Generate stage 1 artifacts
1. Generate stage 2 artifacts

## Cargo Projects

The compiler is split into three different projects:

- `src/libstd` - the standard library
- `src/libtest` - testing support, depends on `libstd`
- `src/rustc` - the compiler

## Build Tools

Tools may be found under `src/tools`, and currently include:

- build-manifest
- cargo
- cargotest
- clippy
- compiletest
- error_index_generator
- linkchecker
- miri
- remote-test-client
- remote-test-server
- rls
- rust-installer
- rustbook
- rustdoc
- rustfmt
- tidy
- unstable-book-gen
