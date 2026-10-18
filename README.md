# taxon-configs

Matching configurations for **Taxon**, an IDA Pro plugin that finds open-source libraries in a binary and names their functions.

A configuration describes one release of one library as one compiler built it: every function's strings, constants, calls, size, stack frame, instruction shape and control-flow shape, the order of the functions in each source file, and the tables of function pointers the library keeps (such as the name-to-function tables of Lua's libraries, or a cipher's list of functions). Taxon identifies a library and its version in a binary, picks the configurations that fit how the binary was built, and matches the library's functions against them.

The configurations are the assets of this repository's [releases](https://github.com/AlonSoko/taxon-configs/releases), one pack per platform; the repository itself holds this description and the manifest of the latest release.

## Packs

| Pack | For binaries built with | Configurations | Size |
|---|---|---|---|
| `taxon-configs-linux-x86.tar.gz` | GCC and clang, x86-64 and x86 (Linux, and MinGW on Windows) | 5,960 | 1.2 GB |
| `taxon-configs-linux-arm64.tar.gz` | GCC and clang, ARM64 | 2,044 | 425 MB |
| `taxon-configs-macos.tar.gz` | Apple clang, Mach-O, ARM64 and x86-64 | 190 | 37 MB |
| `taxon-configs-windows-msvc.tar.gz` | MSVC: Visual Studio 2026 back to Visual C++ 6.0; clang-cl | 1,432 | 292 MB |

Each library release has a configuration for every optimisation level of every compiler and architecture that can build it:

| Compiler | Architectures | Optimisation levels |
|---|---|---|
| GCC 15, 14, 13, 12, 11, 9, 7, 5, 4.8 | x86-64, x86, ARM64 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| GCC 4.4, 4.1, 3.4, 3.3 | x86-64, x86 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| clang 19 to 10, 6.0, 3.8 | x86-64, x86, ARM64 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| Apple clang 21 | ARM64, x86-64 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| MSVC 14.51 (VS 2026), 14.44 (VS 2022), 14.29 (VS 2019), 14.16 (VS 2017), 14.0 (VS 2015), 12.0 (VS 2013), 11.0 (VS 2012), 10.0 (VS 2010), 9.0 (VS 2008) | x86-64 | `/Od`, `/O2`, `/O1` |
| the same, and 8.0 (VS 2005), 7.1 (Visual Studio .NET 2003), 7.0 (Visual Studio .NET 2002), 6.0 (Visual C++ 6.0) | x86 | `/Od`, `/O2` |
| clang-cl 19 (with lld-link) | x86-64, x86 | `/Od`, `/O2` (x86-64 also `/O1`) |

**Link-time optimised builds** (GCC and clang `-flto`, MSVC `/GL`) have configurations of their own, marked `-lto` after the architecture: GCC 4.8 to 15 and clang 6.0 to 19 at `-O2` (GCC 14 and clang 19 also at `-Os` and `-O3`, and ARM64 at `-O2`), and Visual Studio .NET 2002 to Visual Studio 2026 at `/O2` (2019 and 2026 also at `/O1`). Each is the library linked alone with LTO; GCC's also come as `-lto+whole`, linked with only the library's entry points kept, as GCC treats a library inside a program. Taxon tries them only when a library's functions are not laid out file by file.

Taxon picks the compiler release from the binary's identification string (`.comment`) when it keeps one, and the nearest releases' configurations stand in for a release that has none.

The compilers before VS 2015 cannot build every library: the libraries are written in C99 and later, and VS 2012 and older lack its headers (`stdint.h` before VS 2010, `inttypes.h` and `stdbool.h` before VS 2013) and parts of its syntax. The libraries are built with small stand-ins for the missing headers, as old projects were, so VS 2015 and newer build all 18 library releases; VS 2013 17 of them, VS 2012 16, VS 2010 and 2008 15, VS 2005 14, Visual C++ 6.0 8. `manifest.json` lists what each pack has.

A library can come in more than one *flavour*, built with another set of its options: SQLite as `sqlite3.c` is built by default, and as `+distro`, with the options Linux distributions enable (FTS3/4/5, R-Tree, Geopoly, sessions, column metadata, math functions, ...), whose code those builds carry (e.g. `sqlite3_3.46.1@gcc-O2-x64+distro.json.gz`).

`manifest.json` lists every library with its licence, its configured releases and the versions each one covers (a configuration stands in for the other releases of the same major version), the variants in each pack, and each pack's size and SHA-256. The packs are reproducible: the same configurations give byte-identical archives.

## Libraries

| Library | Configured release | Licence |
|---|---|---|
| BearSSL | 0.6 | MIT |
| Bzip2 | 1.0.8 | bzip2-1.0.6 |
| cJSON | 1.7.19 | MIT |
| LibTomCrypt | 1.18.2 | Unlicense OR WTFPL |
| LibTomMath | 1.3.0 | Unlicense |
| Lua | 5.4.7 | MIT |
| lwIP | 2.2.1 | BSD-3-Clause |
| LZ4 | 1.10.0 | BSD-2-Clause |
| LZMA SDK | 25.01 | Public domain |
| mbedTLS | 2.28.10, 3.6.6 | Apache-2.0 OR GPL-2.0-or-later |
| MinHook | 1.3.4 | BSD-2-Clause |
| miniz | 3.1.2 | MIT |
| Mongoose | 7.23 | GPL-2.0-only OR Cesanta commercial |
| SQLite | 3.46.1, 3.54.0 (each plain and `+distro`) | Public domain (blessing) |
| zlib | 1.3.1 | Zlib |
| Zstandard | 1.5.7 | BSD-3-Clause OR GPL-2.0-only |

## Using them

With Taxon installed:

```sh
python -m taxon.configs list                        # the latest release: packs, libraries, licences
python -m taxon.configs fetch                       # every pack
python -m taxon.configs fetch --platform linux-x86  # or only the ones you need
```

`fetch` checks each pack against the manifest's SHA-256 and unpacks it into `~/.taxon/configs` (or `$TAXON_CONFIGS`), where the matcher looks for configurations.

By hand:

```sh
mkdir -p ~/.taxon/configs
curl -L https://github.com/AlonSoko/taxon-configs/releases/latest/download/taxon-configs-linux-x86.tar.gz \
  | tar -xz -C ~/.taxon/configs
```

Each pack unpacks to `<library>/<library>_<version>[_windows]@<compiler>-<optimisation>-<architecture>.json.gz`, e.g. `zlib/zlib_1.3.1@gcc-O2-x64.json.gz`.

## Licence

Each configuration is derived from the compiled code of the library it describes, and is distributed under that library's licence, as listed above and in `manifest.json`. The rest of this repository (this README and the manifest) is under the MIT licence; see [LICENSE](LICENSE).
