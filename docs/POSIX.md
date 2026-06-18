# Building NZBGet on POSIX (Linux, macOS, FreeBSD)

## Prerequisites

### Build System & Compiler

| Tool | Minimum Version | Notes |
|------|----------------|-------|
| [CMake](https://cmake.org/) | 3.20 | Build system |
| [GCC](https://gcc.gnu.org/) | 13.4 | C++20 support |
| [Clang](https://clang.llvm.org/) | 14 | C++20 support (Apple Clang 15 / Xcode 15) |

### Required Libraries

| Library | Purpose |
|---------|---------|
| [libxml2](https://gitlab.gnome.org/GNOME/libxml2/-/wikis/home) | NZB XML parsing |
| [Boost.JSON](https://github.com/boostorg/json) | JSON handling |
| [Boost.Asio](https://github.com/boostorg/asio) | Networking / async I/O |
| [zlib](https://www.zlib.net/) | GZip support (web server & client) |

### Optional Libraries

| Library | Purpose |
|---------|---------|
| [ncurses](https://invisible-island.net/ncurses) | Terminal UI mode (enabled by default) |
| [OpenSSL](https://www.openssl.org) | TLS/SSL encrypted connections |
| [Boost.Test](https://github.com/boostorg/test) | Unit tests |

### Static Code Analysis (Optional)

- [Clang-Tidy](https://clang.llvm.org/extra/clang-tidy/)
- [Cppcheck](https://cppcheck.sourceforge.io/)

> **Note:** You need the development packages for these libraries. Package names often have a `-dev` or `-devel` suffix.

---

## Quick Start

The fastest way to build and run NZBGet:

```bash
# Clone
git clone https://github.com/nzbgetcom/nzbget.git
cd nzbget

# Configure
mkdir build && cd build
cmake ..

# Build (use -j with your CPU core count)
cmake --build . -j "$(nproc)"

# Run
./nzbget -s
```

---

## Installing Dependencies

### Debian / Ubuntu

```bash
# Build essentials and required libraries
apt install cmake build-essential libncurses-dev libssl-dev \
            libxml2-dev zlib1g-dev

# Boost
apt install libboost-json-dev libboost-asio-dev

# For tests
apt install libboost-test-dev

# For static analysis
apt install clang-tidy cppcheck
```

### FreeBSD

```bash
pkg install cmake ncurses openssl libxml2 zlib boost-libs
```

### macOS

```bash
# Install Xcode Command Line Tools
xcode-select --install

# Install dependencies via Homebrew
brew install cmake ncurses openssl libxml2 zlib boost
```

---

## Build Options

Pass these flags to `cmake ..` to customize your build:

| Flag | Description | Default |
|------|-------------|---------|
| `-DCMAKE_BUILD_TYPE=Debug` | Debug build (no optimizations, debug symbols) | `Release` |
| `-DCMAKE_INSTALL_PREFIX=~/usr` | Install to a custom prefix | `/usr/local` |
| `-DENABLE_TESTS=ON` | Build and enable unit tests | `OFF` |
| `-DENABLE_STATIC=ON` | Produce a fully static binary | `OFF` |
| `-DENABLE_SANITIZERS=ON` | Enable leak, address, and undefined-behavior sanitizers | `OFF` |
| `-DENABLE_CLANG_TIDY=ON` | Run Clang-Tidy static analysis during build | `OFF` |
| `-DDISABLE_CURSES=ON` | Disable ncurses terminal UI | `OFF` |
| `-DDISABLE_PARCHECK=ON` | Disable par2 check module | `OFF` |
| `-DDISABLE_TLS=ON` | Disable TLS/SSL support | `OFF` |
| `-DDISABLE_GZIP=ON` | Disable GZip compression | `OFF` |
| `-DDISABLE_SIGCHLD_HANDLER=ON` | Disable SIGCHLD handler (may be needed on 32-bit BSD) | `OFF` |

### Static Build with Custom Link Flags

```bash
export LIBS="-lncurses -ltinfo -lboost_json -lxml2 -lz -lm -lssl -lcrypto \
             -Wl,--whole-archive -lpthread -Wl,--no-whole-archive"
export INCLUDES="/usr/include/;/usr/include/libxml2/"
cmake .. -DENABLE_STATIC=ON
```

---

## Build & Install

```bash
# Configure (with options as needed)
mkdir build && cd build
cmake .. -DCMAKE_BUILD_TYPE=Release

# Build (adjust -j to your CPU core count)
cmake --build . -j "$(nproc)"

# Install to the prefix (default: /usr/local)
cmake --install .

# Install configuration files to <prefix>/etc
cmake --build . --target install-conf

# Uninstall
cmake --build . --target uninstall
cmake --build . --target uninstall-conf
```

---

## Running Tests

```bash
# Configure with tests enabled
cmake .. -DENABLE_TESTS=ON

# Build and run all tests
cmake --build . -j "$(nproc)"
ctest --output-on-failure
```

---

## Static Analysis

### Cppcheck

After configuring the project (which generates `compile_commands.json` in the build directory):

```bash
# Run all checks, suppressing system include noise
cppcheck --project=compile_commands.json --enable=all --suppress=missingIncludeSystem

# Skip a directory (e.g., third-party code)
cppcheck --project=compile_commands.json --enable=all --suppress=missingIncludeSystem -i3rdparty
```

### Clang-Tidy

```bash
cmake .. -DENABLE_CLANG_TIDY=ON
cmake --build . -j "$(nproc)"
```
