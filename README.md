# taxon-configs

Matching configurations for **Taxon**, an IDA Pro plugin that finds open-source libraries in a binary and names their functions.

A configuration describes one release of one library as one compiler built it: every function's strings, constants, calls, size, stack frame, instruction shape and control-flow shape, the structure fields and global data it reaches, and its operands' roles, the order of the functions in each source file, and the tables of function pointers the library keeps (such as the name-to-function tables of Lua's libraries, or a cipher's list of functions). Taxon identifies a library and its version in a binary, picks the configurations that fit how the binary was built, and matches the library's functions against them.

The configurations are the assets of this repository's [releases](https://github.com/AlonSoko/taxon-configs/releases), one pack per library and platform; the repository itself holds this description and the manifest of the latest release. Taxon fetches the packs of each library it finds in a binary by itself, for the binary's platform, so most users never download them by hand.

## Packs

Release 2026.10.26 has 17,332 configurations in 62 packs, 770 MB in all. A pack is named `taxon-configs-<library>-<platform>.tar.zst`, e.g. `taxon-configs-zlib-linux-x86.tar.zst`, for one of four platforms:

| Platform | For binaries built with | Configurations | Size |
|---|---|---|---|
| `linux-x86` | GCC and clang, x86-64 and x86 (Linux, and MinGW on Windows), and MinGW-w64's GCC | 10,926 | 484 MB |
| `linux-arm64` | GCC and clang, ARM64 | 4,149 | 180 MB |
| `macos` | Apple clang, Mach-O, ARM64 and x86-64 | 406 | 19 MB |
| `windows` | MSVC: Visual Studio 2026 back to Visual C++ 6.0; clang-cl | 1,851 | 87 MB |

Each library's packs, size (configurations):

| Library | `linux-x86` | `linux-arm64` | `macos` | `windows` |
|---|---|---|---|---|
| BearSSL | 14.2 MB (592) | 5.4 MB (225) | 0.7 MB (22) | 2.2 MB (77) |
| Bzip2 | 1.7 MB (598) | 0.7 MB (225) | 0.1 MB (22) | 0.4 MB (100) |
| cJSON | 1.3 MB (598) | 0.5 MB (225) | 0.07 MB (22) | 0.3 MB (105) |
| LibTomCrypt | 16.6 MB (598) | 6.5 MB (225) | 0.8 MB (22) | 3.3 MB (105) |
| LibTomMath | 4.3 MB (598) | 1.6 MB (225) | 0.2 MB (22) | 0.8 MB (97) |
| Lua | 19.5 MB (598) | 7.0 MB (225) | 1.0 MB (22) | 4.2 MB (105) |
| lwIP | 8.8 MB (598) | 3.8 MB (225) | 0.5 MB (22) | 2.0 MB (105) |
| LZ4 | 5.0 MB (598) | 1.9 MB (225) | 0.3 MB (22) | 1.1 MB (95) |
| LZMA SDK (`LZMA_SDK`) | 2.3 MB (564) | 0.9 MB (225) | 0.1 MB (22) | 0.6 MB (105) |
| mbedTLS | 74.9 MB (1,127) | 30.8 MB (445) | 3.5 MB (44) | 10.6 MB (118) |
| MinHook | 0.1 MB (108) | — | — | 0.1 MB (95) |
| miniz | 3.4 MB (598) | 1.3 MB (225) | 0.2 MB (22) | 0.7 MB (95) |
| Mongoose | 7.2 MB (598) | 2.9 MB (225) | 0.4 MB (22) | 1.4 MB (75) |
| SQLite (`sqlite3`) | 300.7 MB (1,957) | 108.2 MB (779) | 10.5 MB (76) | 53.8 MB (376) |
| zlib | 3.0 MB (598) | 1.2 MB (225) | 0.2 MB (22) | 0.6 MB (103) |
| Zstandard | 21.1 MB (598) | 6.9 MB (225) | 0.8 MB (22) | 4.6 MB (95) |

A pack is a tar of the library's configurations for the platform in one zstd stream (level 22, 128 MB window), which stores what they share once: one library's configurations across compilers, releases and levels are mostly the same functions, and the whole set is about 5 times smaller than as separately gzipped files.

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
| MinGW-w64 GCC 14, 13, 10, 9, 7, 5 | x86-64, x86 | `-O2`, `-O3`, `-Os` |

**Link-time optimised builds** (GCC and clang `-flto`, MSVC `/GL`) have configurations of their own, marked `-lto` after the architecture: GCC 4.8 to 15, clang 6.0 to 19 and Apple clang at `-O2`, `-Os` and `-O3` (x86-64, x86 and ARM64), MinGW-w64 GCC 5 to 14 at the same levels, and Visual Studio .NET 2002 to Visual Studio 2026 and clang-cl at `/O2` and `/O1`. Each is the library linked alone with LTO; GCC's (MinGW-w64's too) also come as `-lto+whole`, linked with only the library's entry points kept, as GCC treats a library inside a program, and clang's as `-lto+thin`, with ThinLTO. Taxon tries them only when a library's functions are not laid out file by file.

The **MinGW-w64** configurations are built by each release's own MinGW-w64 cross compiler; Taxon tries them for PE files only, alongside the GCC ones.

Taxon picks the compiler release from the binary's identification string (`.comment`) when it keeps one, and the nearest releases' configurations stand in for a release that has none.

The compilers before VS 2015 cannot build every library: the libraries are written in C99 and later, and VS 2012 and older lack its headers (`stdint.h` before VS 2010, `inttypes.h` and `stdbool.h` before VS 2013) and parts of its syntax. The libraries are built with small stand-ins for the missing headers, as old projects were, so VS 2015 and newer build all 18 library releases; VS 2013 17 of them, VS 2012 16, VS 2010 and 2008 15, VS 2005 14, Visual C++ 6.0 8. `manifest.json` lists what each pack has.

A library can come in more than one *flavour*, built with another set of its options: SQLite as `sqlite3.c` is built by default, and as `+distro`, with the options Linux distributions enable (FTS3/4/5, R-Tree, Geopoly, sessions, column metadata, math functions, ...), whose code those builds carry (e.g. `sqlite3_3.46.1@gcc-O2-x64+distro.json.gz`).

`manifest.json` (format 2) lists every library with its licence, its configured releases and the versions each one covers (a configuration stands in for the other releases of the same major version), the variants of each release per platform, and each pack's size, number of configurations and SHA-256. The packs are reproducible: the same configurations give byte-identical archives.

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

Taxon fetches what it needs: the first time it finds a library in a binary, it downloads that library's pack for the binary's platform from the latest release, checks it against the manifest's SHA-256 and unpacks it into `~/.taxon/configs` (or `$TAXON_CONFIGS`), where the matcher looks for configurations (`TAXON_FETCH=0` turns this off). To fetch ahead, for offline use:

```sh
python -m taxon.configs list                                  # the latest release: packs, libraries, licences
python -m taxon.configs fetch                                 # every pack
python -m taxon.configs fetch --platform linux-x86            # one platform
python -m taxon.configs fetch --library zlib --library SQLite # some libraries
python -m taxon.configs update                                # the installed packs, from the newest release
```

The packs need a Taxon that reads manifest format 2; an older one stops with a message to update Taxon. Unpacking needs Python 3.14, or the `zstandard` package before it (a dependency of Taxon there).

By hand:

```sh
mkdir -p ~/.taxon/configs
curl -L https://github.com/AlonSoko/taxon-configs/releases/latest/download/taxon-configs-zlib-linux-x86.tar.zst \
  | zstd -d --long=27 | tar -x -C ~/.taxon/configs
```

Each pack unpacks to `<library>/<library>_<version>[_windows]@<compiler>-<optimisation>-<architecture>.json`, e.g. `zlib/zlib_1.3.1@gcc-O2-x64.json`.

## Licence

Each configuration is derived from the compiled code of the library it describes, and is distributed under that library's licence, as listed above and in `manifest.json`. The rest of this repository (this README and the manifest) is under the MIT licence; see [LICENSE](LICENSE).
