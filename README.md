# taxon-configs

Matching configurations for **Taxon**, an IDA Pro plugin that finds open-source libraries in a binary and names their functions.

A configuration describes one release of one library as one compiler built it: every function's strings, constants, calls, size, stack frame, instruction shape and control-flow shape, and the order of the functions in each source file. Taxon identifies a library and its version in a binary, picks the configurations that fit how the binary was built, and matches the library's functions against them.

The configurations are the assets of this repository's [releases](https://github.com/AlonSoko/taxon-configs/releases), one pack per platform; the repository itself holds this description and the manifest of the latest release.

## Packs

| Pack | For binaries built with | Configurations | Size |
|---|---|---|---|
| `taxon-configs-linux-x86.tar.gz` | GCC and clang, x86-64 and x86 (Linux, and MinGW on Windows) | 320 | 39 MB |
| `taxon-configs-linux-arm64.tar.gz` | GCC and clang, ARM64 | 160 | 19 MB |
| `taxon-configs-macos.tar.gz` | Apple clang, Mach-O, ARM64 and x86-64 | 160 | 18 MB |
| `taxon-configs-windows-msvc.tar.gz` | MSVC: Visual Studio 2026 and 2019 | 170 | 21 MB |

Each library release has a configuration for every optimisation level of every compiler and architecture:

| Compiler | Architectures | Optimisation levels |
|---|---|---|
| GCC 14 | x86-64, x86, ARM64 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| clang 19 | x86-64, x86, ARM64 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| Apple clang 21 | ARM64, x86-64 | `-O0`, `-O1`, `-O2`, `-O3`, `-Os` |
| MSVC 14.51 (VS 2026), MSVC 14.29 (VS 2019) | x86-64 | `/Od`, `/O2`, `/O1` |
| MSVC 14.51 (VS 2026), MSVC 14.29 (VS 2019) | x86 | `/Od`, `/O2` |

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
| SQLite | 3.54.0 | Public domain (blessing) |
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
