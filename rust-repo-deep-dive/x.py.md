# `x.py` - the build system

Rust uses Python for managing its build logic, and the `x.py` is the script in charge. Or maybe it is more of a figurehead, since it completely delegates all work to the `bootstrap` module. Before diving into that, the options I have been exposed to are:

- `build` for building rust
- `install` for installing to location specified by `config.toml`
- `test` for building in test mode and running tests
- `--stage N` for limiting build stages since full builds are costly

## Python for build tools

Python is a good choice for the bootstrap build tool. Python is available on most systems, powerful, expressive, and likely familiar for many programmers. I believe the Rust core team uses Python for these reasons, but they are not the only ones. Some members of the C and C++ communities aslo lean on Python for build and dependency management. For instance, Conan.io is a package manager intended for C and C++ code, which uses Python internally and publicly for its users. However, the Rust use of Python is only for bootstrap purposes until Cargo is safely available. By contrast, the C and C++ communities are continually dependent on Python because a dominant package manager has not surfaced.