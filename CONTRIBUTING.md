# Contributing to llvm-3.4.2-ppc-leopard

Thanks for contributing to the PowerPC Leopard LLVM 3.4.2 package. This
repository documents and distributes a vintage Mac OS X toolchain, so changes
should preserve reproducibility and clear install instructions.

## Setup

1. Fork the repository and create a branch:

   ```sh
   git checkout -b fix/short-description
   ```

2. Review `README.md` before changing install commands, package layout, or
   compiler usage examples.

3. On a PowerPC Leopard system, install the release package as documented:

   ```sh
   curl -LO https://github.com/Scottcjn/llvm-3.4.2-ppc-leopard/releases/download/v3.4.2/llvm-3.4.2-ppc-leopard.tar.gz
   sudo tar xzf llvm-3.4.2-ppc-leopard.tar.gz -C /usr/local
   ```

4. Validate basic compiler behavior where hardware is available:

   ```sh
   clang -O2 -o hello hello.c
   ./hello
   clang -maltivec -O3 -o vectorized simd_code.c
   ```

## Pull Request Guidelines

- Keep documentation, packaging metadata, and compatibility updates separate
  when possible.
- State the Mac OS X version, CPU model, and compiler command tested.
- Include release artifact details if package names, paths, or checksums change.
- Update examples when supported flags, include paths, or target triples change.
- Do not commit extracted release trees, generated binaries, or local build logs.

## Code Style

- Keep command examples copy-pasteable on Mac OS X Leopard.
- Use precise target names such as `powerpc-apple-darwin9.8.0`.
- Preserve compatibility notes for GCC and libstdc++ include paths.
- Avoid modern shell or tooling assumptions in instructions meant for Leopard.
- Tie performance or compatibility claims to a tested command or documented
  source.

## Validation Checklist

- [ ] Install commands were checked against the current release artifact.
- [ ] Compiler examples were run on PowerPC Leopard, or the limitation is noted.
- [ ] README changes preserve existing package paths and target naming.
- [ ] No generated binaries, extracted archives, or build logs are committed.
- [ ] The PR describes the hardware and OS version used for validation.
