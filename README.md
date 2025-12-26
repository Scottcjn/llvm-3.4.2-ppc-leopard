# LLVM/Clang 3.4.2 for PowerPC G5 (Mac OS X Leopard)

Pre-built LLVM and Clang binaries for PowerPC G5 Macs running Mac OS X 10.5 Leopard.

## Quick Install

```bash
# Download and extract
curl -LO https://github.com/Scottcjn/llvm-3.4.2-ppc-leopard/releases/download/v3.4.2/llvm-3.4.2-ppc-leopard.tar.gz
sudo tar xzf llvm-3.4.2-ppc-leopard.tar.gz -C /usr/local

# Add to PATH
export PATH=/usr/local/bin:$PATH
```

## System Requirements

- Mac OS X 10.5 Leopard (Darwin 9.x)
- PowerPC G5 processor
- GCC 7.5.0 for C++ standard library headers (optional)

## Included Binaries

| Binary | Description |
|--------|-------------|
| `clang` / `clang++` | C/C++ compiler |
| `llc` | LLVM static compiler |
| `lli` | LLVM interpreter |
| `llvm-ar` | Archive tool |
| `llvm-as` / `llvm-dis` | Assembler/Disassembler |
| `opt` | Optimizer |
| `clang-format` | Code formatter |

## Verified Working

| Feature | Status |
|---------|--------|
| C compilation | ✅ Works |
| AltiVec/VMX | ✅ Works (`-maltivec`) |
| C++ core (templates, classes) | ✅ Works |
| C++ stdlib (iostream, vector) | ⚠️ Needs GCC 7 headers |

## Usage Examples

### C Program
```bash
clang -O2 -o hello hello.c
```

### AltiVec Vectorization
```bash
clang -maltivec -O3 -o vectorized simd_code.c
```

### C++ with GCC 7 Standard Library
```bash
clang++ -std=c++11 \
  -I/usr/local/gcc-7.5.0/include/c++/7.5.0 \
  -I/usr/local/gcc-7.5.0/include/c++/7.5.0/powerpc-apple-darwin8.11.0 \
  -L/usr/local/gcc-7.5.0/lib \
  -o program program.cpp
```

## Build Info

- **LLVM Version**: 3.4.2 (tags/RELEASE_34/dot2-final)
- **Target**: powerpc-apple-darwin9.8.0
- **Built with**: GCC 4.0.1 (Apple)
- **Build date**: December 2025

## Also Available

- GCC 7.5.0 for PPC Leopard (via Tigerbrew)
- GCC 10.5.0 for PPC Leopard (custom build)

## License

LLVM is distributed under the University of Illinois/NCSA Open Source License.
See LICENSE.TXT in the LLVM source for details.
