# Contributing to LLVM/Clang 3.4.2 for PowerPC Leopard

Thank you for your interest in contributing to the LLVM/Clang 3.4.2 PowerPC Leopard project! This repository provides pre-built binaries of LLVM and Clang 3.4.2 for PowerPC G5 Macs running Mac OS X Leopard (10.5).

## Table of Contents

- [Code of Conduct](#code-of-conduct)
- [Getting Started](#getting-started)
- [Development Environment](#development-environment)
- [How to Contribute](#how-to-contribute)
- [Build Instructions](#build-instructions)
- [Testing](#testing)
- [Style Guidelines](#style-guidelines)
- [Submitting Changes](#submitting-changes)
- [Community](#community)

## Code of Conduct

This project adheres to a code of conduct that expects all participants to:

- Be respectful and inclusive
- Welcome newcomers and help them learn
- Focus on constructive feedback
- Respect the vintage nature of the hardware we're supporting

## Getting Started

### Prerequisites

To contribute to this project, you'll need:

1. **PowerPC G5 Mac** or QEMU emulation environment
2. **Mac OS X Leopard (10.5.8)** or compatible system
3. **Xcode 3.1.4** (for original toolchain)
4. **Basic knowledge of C/C++** and compiler construction
5. **Understanding of PowerPC architecture** (G5/PPC970)

### Understanding the Project

This project maintains:

- Pre-built LLVM/Clang 3.4.2 binaries for PowerPC Leopard
- Patches for PowerPC-specific issues
- Build scripts and documentation
- Compatibility layers for modern C++ features

Key technical details:

- **Target Architecture**: PowerPC G5 (PPC970FX/MP)
- **Target OS**: Mac OS X Leopard 10.5.8
- **Base LLVM Version**: 3.4.2
- **Base Clang Version**: 3.4.2
- **ABI**: PowerPC 32-bit and 64-bit (via `-m64`)

## Development Environment

### Option 1: Real Hardware (Recommended)

If you have access to a PowerPC G5 Mac:

```bash
# Verify your system
uname -m  # Should output "Power Macintosh"
system_profiler SPHardwareDataType | grep "Processor Name"
```

### Option 2: QEMU Emulation

For development without physical hardware:

```bash
# Install QEMU (on modern macOS/Linux)
brew install qemu  # macOS
# or
sudo apt-get install qemu-system-ppc  # Ubuntu/Debian

# Create QEMU disk image
qemu-img create -f qcow2 leopard.qcow2 20G

# Boot Leopard ISO (requires original install media)
qemu-system-ppc -M mac99 -m 1024 \
  -hda leopard.qcow2 \
  -cdrom leopard_install.iso \
  -boot d
```

### Setting Up Build Tools

```bash
# Install required dependencies
# On Leopard with MacPorts:
sudo port install cmake
sudo port install python27

# Or using Tigerbrew (recommended for 10.5):
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/mistydemeo/tigerbrew/go/install)"
brew install cmake
brew install python
```

## How to Contribute

### Types of Contributions

We welcome the following types of contributions:

1. **Bug Reports**: Issues with the pre-built binaries
2. **Patches**: Fixes for PowerPC-specific problems
3. **Documentation**: Improvements to build instructions
4. **Build Scripts**: Automation for building LLVM/Clang
5. **Test Cases**: Validation for compiler correctness
6. **Performance Optimizations**: G5-specific improvements

### Finding Issues to Work On

Check our issue tracker for:

- `good-first-issue`: Beginner-friendly tasks
- `help-wanted`: Specific assistance needed
- `powerpc-specific`: PowerPC architecture issues
- `documentation`: Documentation improvements
- `build-system`: Build script enhancements

## Build Instructions

### Building LLVM/Clang 3.4.2 from Source

```bash
# 1. Clone the LLVM source
git clone https://github.com/llvm/llvm-project.git --branch release/3.4 --depth 1
cd llvm-project

# 2. Apply PowerPC Leopard patches
git apply ../patches/leopard-powerpc-fixes.patch

# 3. Create build directory
mkdir build && cd build

# 4. Configure with CMake
cmake -G "Unix Makefiles" \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_INSTALL_PREFIX=/usr/local/llvm-3.4.2 \
  -DLLVM_TARGETS_TO_BUILD="PowerPC" \
  -DLLVM_ENABLE_ASSERTIONS=OFF \
  -DLLVM_ENABLE_ZLIB=OFF \
  -DLLVM_ENABLE_TERMINFO=OFF \
  ..

# 5. Build (this will take several hours on G5)
make -j$(sysctl -n hw.ncpu)

# 6. Install
sudo make install
```

### Cross-Compilation from Intel Mac

If building on a modern Intel Mac for PowerPC:

```bash
# Install osxcross for cross-compilation
git clone https://github.com/tpoechtrager/osxcross
cd osxcross

# Build SDK (requires Xcode 3.1.4 DMG)
./build.sh

# Set up environment
export PATH="$PWD/target/bin:$PATH"
export CC=powerpc-apple-darwin9-gcc
export CXX=powerpc-apple-darwin9-g++

# Configure LLVM for cross-compilation
cmake -G "Unix Makefiles" \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_SYSTEM_NAME=Darwin \
  -DCMAKE_SYSTEM_VERSION=9.8.0 \
  -DCMAKE_SYSTEM_PROCESSOR=powerpc \
  -DCMAKE_C_COMPILER=powerpc-apple-darwin9-gcc \
  -DCMAKE_CXX_COMPILER=powerpc-apple-darwin9-g++ \
  -DLLVM_TARGETS_TO_BUILD="PowerPC" \
  ..
```

## Testing

### Running the Test Suite

```bash
# Run LLVM tests
cd build
make check-llvm

# Run Clang tests
make check-clang

# Run specific PowerPC tests
make check-llvm-codegen-powerpc
```

### Manual Testing

Test your changes with real code:

```bash
# Create test file
cat > test.cpp << 'EOF'
#include <iostream>

int main() {
    std::cout << "Hello, PowerPC G5!" << std::endl;
    return 0;
}
EOF

# Compile with built clang
/usr/local/llvm-3.4.2/bin/clang++ -o test test.cpp

# Run test
./test
```

### PowerPC-Specific Tests

Test AltiVec support:

```cpp
#include <altivec.h>

vector float test_altivec(vector float a, vector float b) {
    return vec_add(a, b);
}

int main() {
    vector float v1 = {1.0, 2.0, 3.0, 4.0};
    vector float v2 = {5.0, 6.0, 7.0, 8.0};
    vector float result = test_altivec(v1, v2);
    return 0;
}
```

Compile with:
```bash
/usr/local/llvm-3.4.2/bin/clang++ -maltivec -o altivec_test altivec_test.cpp
```

## Style Guidelines

### C/C++ Code Style

Follow LLVM coding standards:

```cpp
// Use LLVM naming conventions
class PowerPCSubtarget {
public:
    bool hasAltiVec() const { return HasAltiVec; }
    
private:
    bool HasAltiVec;
};

// Indent with 2 spaces
void function() {
  if (condition) {
    doSomething();
  }
}
```

### Patch Format

When submitting patches:

```bash
# Create patch from git
git format-patch -1 HEAD

# Or create unified diff
diff -u original.c modified.c > fix.patch
```

Patch requirements:
- Use unified diff format (`-u`)
- Include descriptive commit messages
- Reference related issues
- Keep patches focused and minimal

### Shell Script Style

For build scripts:

```bash
#!/bin/bash
set -euo pipefail

# Variables in UPPER_CASE
readonly LLVM_VERSION="3.4.2"
readonly BUILD_DIR="build"

# Functions in lower_case
build_llvm() {
    local target="$1"
    echo "Building for target: ${target}"
    # ...
}

# Main execution
main() {
    build_llvm "powerpc-apple-darwin9"
}

main "$@"
```

## Submitting Changes

### Pull Request Process

1. **Fork the repository** on GitHub
2. **Clone your fork** locally
3. **Create a feature branch**: `git checkout -b feature/your-feature`
4. **Make your changes** with clear, focused commits
5. **Test thoroughly** on PowerPC hardware or QEMU
6. **Update documentation** if needed
7. **Submit a pull request** with detailed description

### PR Checklist

Before submitting:

- [ ] Code compiles without warnings on PowerPC Leopard
- [ ] All tests pass (`make check`)
- [ ] New features include tests
- [ ] Documentation is updated
- [ ] Commit messages are clear and descriptive
- [ ] Changes are focused and minimal

### Commit Message Format

```
component: Brief description (50 chars or less)

More detailed explanation if needed. Wrap at 72 characters.
Include references to issues: Fixes #123, Closes #456

- Bullet points are okay
- Use imperative mood ("Add feature" not "Added feature")
```

Examples:
```
powerpc: Fix AltiVec vector initialization on G5

The vec_splat function was generating incorrect code for
floating-point vectors on PPC970. This patch ensures proper
alignment and initialization.

Fixes #42
```

## Community

### Getting Help

- **GitHub Issues**: Bug reports and feature requests
- **Discussions**: General questions and ideas
- **Wiki**: Additional documentation and tips

### Resources

- [LLVM Documentation](https://llvm.org/docs/)
- [Clang Documentation](https://clang.llvm.org/docs/)
- [PowerPC Architecture](https://www.ibm.com/docs/en/aix/7.2?topic=overview-powerpc-architecture)
- [Mac OS X Leopard Development](https://developer.apple.com/library/archive/navigation/)

### Acknowledgments

Thank you to:
- The LLVM project for the original compiler infrastructure
- The PowerPC community for keeping G5 hardware alive
- Contributors who help maintain Leopard compatibility

---

**Happy hacking on vintage hardware!** 🍎⚡️