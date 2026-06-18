# Building NZBGet on Windows

## Prerequisites

### Build Tools

- [CMake](https://cmake.org/)
- [Visual Studio 2022 Build Tools](https://visualstudio.microsoft.com/downloads/?q=build+tools#build-tools-for-visual-studio-2022)

During installation of *Build Tools for Visual Studio 2022*, select **Desktop development with C++** and ensure these components are included:

- MSVC v143 — VS 2022 C++ x64/x86 build tools (C++20 support)
- Windows 11 SDK
- C++ ATL for latest v143 build tools
- C++ MFC for latest v143 build tools

After installation, add the MSBuild path to your `PATH` environment variable, e.g.:

```
C:\Users\<user>\AppData\Local\Programs\Microsoft VS Code\bin\
```

---

## Dependencies

### Required Libraries

| Library | Purpose |
|---------|---------|
| [OpenSSL](https://www.openssl.org) | TLS/SSL support |
| [libxml2](https://gitlab.gnome.org/GNOME/libxml2/-/wikis/home) | NZB XML parsing |
| [zlib](https://gnuwin32.sourceforge.net/packages/zlib.htm) | GZip support |
| [Boost.JSON](https://github.com/boostorg/json) | JSON handling |
| [Boost.Asio](https://github.com/boostorg/asio) | Networking / async I/O |

### For Tests

| Library | Purpose |
|---------|---------|
| [Boost.Test](https://github.com/boostorg/test) | Unit test framework |

---

## Installing Dependencies with vcpkg

[**vcpkg**](https://vcpkg.io/) is the recommended way to manage dependencies on Windows.

### 1. Install vcpkg

```powershell
# Clone to C:\ (recommended location)
git clone --depth 1 https://github.com/microsoft/vcpkg.git C:\vcpkg

# Bootstrap
C:\vcpkg\bootstrap-vcpkg.bat
```

Add `C:\vcpkg` to your `PATH` environment variable.

### 2. Install Libraries

#### For x64 (64-bit) builds:

```powershell
vcpkg install openssl:x64-windows-static
vcpkg install libxml2:x64-windows-static
vcpkg install zlib:x64-windows-static
vcpkg install boost-json:x64-windows-static
vcpkg install boost-asio:x64-windows-static
```

#### For x86 (32-bit) builds:

```powershell
vcpkg install openssl:x86-windows-static
vcpkg install libxml2:x86-windows-static
vcpkg install zlib:x86-windows-static
vcpkg install boost-json:x86-windows-static
vcpkg install boost-asio:x86-windows-static
```

#### For tests (add to either of the above):

```powershell
vcpkg install boost-test:x64-windows-static
```

---

## Building

### 1. Configure

#### x64 (64-bit):

```powershell
mkdir build
cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake `
         -DVCPKG_TARGET_TRIPLET=x64-windows-static -A x64
```

#### x86 (32-bit):

```powershell
mkdir build
cd build
cmake .. -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake `
         -DVCPKG_TARGET_TRIPLET=x86-windows-static -A Win32
```

### 2. Build

```powershell
# Release build
cmake --build . --config Release

# Debug build
cmake --build . --config Debug
```

Binaries will be in the `Release\` or `Debug\` directory.

---

## Build Options

| Flag | Description |
|------|-------------|
| `-DDISABLE_TLS=ON` | Disable TLS/SSL (use if OpenSSL is unavailable) |
| `-DENABLE_TESTS=ON` | Build and enable unit tests |

### Debug Build Configuration

```powershell
cmake .. -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake `
         -DVCPKG_TARGET_TRIPLET=x64-windows-static `
         -DCMAKE_BUILD_TYPE=Debug
cmake --build . --config Debug
```

---

## Running Tests

```powershell
# Configure with tests enabled
cmake .. -DENABLE_TESTS=ON `
         -DCMAKE_TOOLCHAIN_FILE=C:/vcpkg/scripts/buildsystems/vcpkg.cmake `
         -DVCPKG_TARGET_TRIPLET=x64-windows-static

# Build
cmake --build . --config Release

# Run tests
ctest -C Release

# Debug build tests
ctest -C Debug
```
