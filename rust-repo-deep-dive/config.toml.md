# `config.toml` - the config file

An example configuration file is available under `config.toml.example` which has all options commented out with their default values

## Options

### llvm

- enabled = true
- optimize = true
- release-debuginfo = false
- assertions = false
- ccache = false
- version-check = true
- static-libstdcpp = false
- ninja = false
- targets = "X86;ARM;AArch64;Mips;PowerPC;..."
- experimental-targets = ""
- link-jobs = 0
- link-shared = false

### build

- build = "x86_64-unknown-linux-gnu"
- host = ["x86_64-unknown-linux-gnu"]
- target = ["x86_64-unknown-linux-gnu"]
- cargo = "path/instead/of/stage0"
- rustc = "path/instead/of/stage0"
- docs = true
- compiler-docs = false
- submodules = true
- gdb = "gdb"
- nodejs = "node"
- python = "python2.7"
- locked-deps = false
- vendor = false
- full-bootstrap = false
- extended = false
- verbose = 0
- sanitizers = false
- profiler = false
- openssl-static = false
- low-priority = false
- configure-args = []
- local-rebuild = false

### install

- prefix = "/usr/local"
- sysconfdir = "/etc"
- docdir = "share/doc/rust"
- bindir = "bin"
- libdir = "lib"
- mandir = "share/man"
- datadir = "share"
- infodir = "share/info"
- localstatedir = "/var/lib"

### rust

- debug = false
- optimize = true
- codegen-units = 1
- debug-assertions = false
- debuginfo = false
- debuginfo-lines = false
- debuginfo-only-std = false
- use-jemalloc = true
- debug-jemalloc = false
- backtrace = true
- default-linker = "cc"
- channel = "dev"
- rpath = true
- quiet-tests = false
- optimize-tests = true
- debuginfo-tests = true
- codegen-tests = true
- ignore-git = true
- dist-src = false
- test-miri = false

### dist

- sign-folder = "path/to/folder/to/sign"
- gpg-password-file = "path/to/gpg/password"
- upload-addr = "https://example.com/folder"
- src-tarball = true