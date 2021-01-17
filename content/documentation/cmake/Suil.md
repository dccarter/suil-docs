---
title: Suil
weight: -20
---

{{< toc >}}

The scirpt `Suil.cmake` provide functions that abstract most of the CMake code need to setup a target that uses `SCF`. The script is installed with other CMake artifacts and need to be included in target project as follows

```cmake
include(Suil)
```

# `SuilProject`
The API is used to initialize a target `SCF` project. When invoked the API might setup some environment variables on the parent scope depending on the provided parameters

## Usage
```cmake
SuilProject(name ...)
```
Where `name` is the name of the project. The parameters that are available are documented below
| Parameter | Description|
|:----------|------------|
| **`VERSION`** | This is used to configure the version of the project (default: `CMAKE_PROJECT_VERSION`) |
| **`SCC_SOURCES`** | A list of project level `.scc` source files that need to be transpiled by  `scc`(_optional_) |
| **`SCC_OUT_DIR`** | A project level directory in which `scc` will transpile the given `SCC_SOURCES` into (default: `${CMAKE_BINARY_DIR}/scc/public`) |

# `SuilTarget`
This API is used to create a target and initialize it for dependency on provided `SCF` libraries. This function also abstract a lot of code that would be written to configure a target.

## Usage
```cmake
SuilTarget(name ...)
```
Where `name` is the name of the project. The parameters that are available are documented below
| Parameter | Description |
|:----------|:------------|
| **`TEST`** (`ON`\|`OFF`) | If set to `ON`, the target created will be an executable test binary (default `OFF`) |
| **`LIBRARY`** (`SHARED`\|`STATIC`) | If set, the target created will be a library with the given type. (default: not set) |
| **`SCC_OUTDIR`** | If directory in which `scc` will transpile `.scc` sources to if `SCC_SOURCES` was provide. If this directory is not provide the project level directory with target name appended is used (i.e `${PROJECT_SCC_DIR}/name`) |