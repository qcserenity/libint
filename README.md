# Libint tarballs for Serenity

Preconfigured [libint](https://github.com/evaleev/libint) libraries for [Serenity](https://github.com/qcserenity/serenity). The tarballs are attached to the [releases](https://github.com/qcserenity/libint/releases).

Each tarball is an unmodified Libint export after configuration (so code generation has been done already).

## Available tarballs

| release tag | Libint | generated | size | SHA256 |
|-------------|--------|-----------|------|--------|
| [`libint-2.13.1-serenity`](https://github.com/qcserenity/libint/releases/tag/libint-2.13.1-serenity) | v2.13.1 (git `5fb07b48`) | 2026-11-04 | 190 MiB | `9013b8707d80c79cc28e95f576e337945a9c8d861e8ee7926fd9157b5260c615` |

## Configuration

### `libint-2.13.1-serenity`

| option | value | meaning |
|--------|-------|---------|
| `LIBINT2_ENABLE_ONEBODY` | `1` | one-electron integrals and their first derivatives |
| `LIBINT2_DISABLE_ONEBODY_PROPERTY_DERIVS` | `OFF` | include derivatives of multipole integrals |
| `LIBINT2_ENABLE_ERI` / `ERI3` / `ERI2` | `1` | 4-, 3- and 2-center ERIs with first derivatives |
| `LIBINT2_MAX_AM` | `6;5` | up to i functions for energies, h functions for first derivatives |
| `LIBINT2_OPT_AM` | `4;4` | optimized code up to g functions |
| `LIBINT2_GENERATE_FMA` | `ON` | emit FMA instructions where the target supports them |
| `LIBINT2_ENABLE_GENERIC_CODE` | `ON` | generic code for high angular momenta (smaller tarball) |

All other options are left at the Libint defaults (e.g. `LIBINT2_MULTIPOLE_MAX_ORDER=4`, standard
solid-harmonics ordering, no G12, small loop unrolling).

If you use Libint, please cite it as described in its [`CITATION`](https://github.com/evaleev/libint/blob/master/CITATION) file.

## Generating a tarball

Requirements: CMake >= 3.21, a C++17 compiler, Boost >= 1.57 headers, and GMP with C++ bindings
(`gmpxx`). Ninja is optional. Generation runs on a single thread and took about 16 minutes for
`libint-2.13.1-serenity` (AMD EPYC 7643, g++ 11.3).

```bash
git clone --branch v2.13.1 --depth 1 https://github.com/evaleev/libint.git libint-src
cmake -S libint-src -B libint-gen -G Ninja \
  -DCMAKE_BUILD_TYPE=Release -DBUILD_TESTING=OFF \
  -DLIBINT2_ENABLE_ONEBODY=1 -DLIBINT2_DISABLE_ONEBODY_PROPERTY_DERIVS=OFF \
  -DLIBINT2_ENABLE_ERI=1 -DLIBINT2_ENABLE_ERI3=1 -DLIBINT2_ENABLE_ERI2=1 \
  "-DLIBINT2_MAX_AM=6;5" "-DLIBINT2_OPT_AM=4;4" \
  -DLIBINT2_GENERATE_FMA=ON -DLIBINT2_ENABLE_GENERIC_CODE=ON -DLIBINT2_ENABLE_UNROLLING=100
  # optionally -DBOOST_ROOT=... and -DCMAKE_CXX_COMPILER=...
cmake --build libint-gen --target export   # generates the code and packs the tarball
cp libint-gen/libint-2.13.1-post999.tgz libint-2.13.1-serenity.tgz
sha256sum libint-2.13.1-serenity.tgz
```
