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
### Inputs
Where `name` is the name of the project. The parameters that are available are documented below
| Parameter | Description|
|:----------|------------|
| **`EXPORTS`** (`ON`\|`OFF`) | If set, the project will be configured to use modern CMake when installing libraries. See [`CMakePackageConfigHelpers`](https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html) documentation from CMake (default: `OFF`) |
| **`VERSION`** | This is used to configure the version of the project (default: `CMAKE_PROJECT_VERSION`) |
| **`SCC_SOURCES`** | A list of project level `.scc` source files that need to be transpiled by  `scc`. Should the project be exported, generated header files generated from these sources will also be installed (_optional_) |
| **`PRIV_SCC_SOURCES`** | These are project level private `.scc` sources that `scc` transpile into `${SCC_OUT_DIR}/private`. Header files generated from these sources will not be installed. | 
| **`SCC_OUTDIR`** | A project level directory in which `scc` will transpile the given `SCC_SOURCES` into (default: `${CMAKE_BINARY_DIR}/scc`) |
### Outputs
| Variable | Description |
|:---------|:------------|
| **`SUIL_PROJECT_VERSION`** | The configure project level version (See input `VERSION`) |
| **`SUIL_SCC_PLUGINS_DIR`**| Configured `scc` plugins directory. This is a list of directory which SCC will search for plugins. Set to `${CMAKE_BINARY_DIR}` and `${SUIL_BASE_PATH}/lib` |
| **`SUIL_PROJECT_SCC_OUTDIR`** | This is the root project level directory that `scc` will use to generate it's sources. It points to the input `SCC_OUTDIR` |
| **`SUIL_PROJECT_SCC_PUB`**| The directory into which `scc` generates the given project level public sources (value `${SUIL_PROJECT_SCC_OUTDIR}/public`) |
| **`SUIL_PROJECT_PRIV_PUB`**| The directory into which `scc` generates the given project level private sources (value `${SUIL_PROJECT_SCC_OUTDIR}/private`) |
| **`NAMESPACE`** | This is the namespace that will be used for exported targets. It is set to named parameter `name` |
| **`TARGETS_EXPORT_NAME`** | The name of the Targets export which is set to `${name}Targets` |

## Example (How it works)
```cmake
SuilProject(hello-lib
    EXPORTS ON
    VERSION ${CMAKE_PROJECT_VERSION}
    SCC_SOURCES scc/pub/hello.scc
    PRIV_SCC_SOURCES scc/priv/debug.scc)

SuilShared(hello
    SOURCES ${SUIL_PROJECT_SCC_PUB}/hello.cpp
            ${SUIL_PROJECT_SCC_PRIV}/debug.cpp
    INSTALL ON)
```
This will initialize a project named `hello-lib` and configures it as a relocatable package when installed (`EXPORTS ON`).<br>
The project also configures project level public `SCC_SOURCES` and private `PRIV_SCC_SOURCES`. These sources will be transpiled and it's up to the user to add the generated sources to their targets by using the output variables `SUIL_PROJECT_PRIV_(PRIV|PUB)`

# `SuilTarget`
This API is used to create a target and initialize it for dependency on provided `SCF` libraries. This function also abstract a lot of code that would be written to configure a target.

## Usage
```cmake
SuilTarget(name ...)
```
Where `name` is the name of the project. The parameters that are available are documented below

### Inputs
| Parameter | Description |
|:----------|:------------|
| **`TEST`** (`ON`\|`OFF`) | If set to `ON`, the target created will be an executable test binary (default `OFF`) |
| **`LIBRARY`** (`SHARED`\|`STATIC`) | If set, the target created will be a library with the given type. (default: not set) |
| **`SCC_OUTDIR`** | If directory in which `scc` will transpile `.scc` sources to if `SCC_SOURCES` was provide. If this directory is not provide the project level directory with target name appended is used (i.e `${PROJECT_SCC_DIR}/name`) |
| **`DEPENDS`** | A list of other CMake targets defined for this current project that the target being defined needs. |
| **`VERSION`** | The version of the target being defined. If this is not specified the version defined (or defaulted) invoking `SuilProject` will be used. This version is added as a define to the target as either `LIB_VERSION` or `APP_VERSION` |
| **`SOURCES`** | The list of source files need that make up the target. If source files are not, this function will recursively look for `C`/`C++` source files under directory `src` (i.e all files that end in `.c`,`cc` and `.cpp` extension. <br> _CMake recommends manually specifying target source files and this recommendation is also applicable here._ |
| **`LIBRARIES`**  | A list of libraries that this target needs for compilation or linking against. The can be `SCF` libraries (e.g `Suil::Base`), CMake package libraries found using `find_package(...)` or system libraries (e.g `math` for `-lmath`).<br> If the package is a library, the are added to the library as `PUBLIC` libraries and will be required to link against the target. |
| **`PRIV_LIBS`** | A list of libraries that will be added to the target library as `PRIVATE`. This are the libraries that the target needs to build but are not needed when linking against the target. This is only useful if the target is a library |
| **`INF_LIBS`** | Interface libraries are only only added to the targets `INTERFACE_LINK_LIBRARIES` but are not used for linking the target. These are need only to link against the target. Also only useful for exported targets |
| **`PRIV_INCS`** | A list of include directories needed to compile the current target but not needed by users of the target. This option only useful on exported targets. See [CMake `target_include_directories`](https://cmake.org/cmake/help/latest/command/target_include_directories.html) for more info |
| **`INCLUDES`** | A list of `PUBLIC` directories that will be added to the target with [`target_include_directories`](https://cmake.org/cmake/help/latest/command/target_include_directories.html). <br> Note that if this is not configured, this API will try to default to the directory `${CMAKE_CURRENT_SOURCE_DIR}/$include/${name}` |
| **`INF_INCS`** | A list of include directories needed to compile a target that links against the current target but not needed to build the current target. [CMake `target_include_directories`](https://cmake.org/cmake/help/latest/command/target_include_directories.html) documents this better |
| **`DEFINES`** | A list of defines that will be onto the target. These are always added as `PUBLIC` definitions and will be added to the  `INTERFACE_COMPILE_DEFINITIONS` property of the exported target |
| **`PRIV_DEFS`** | A list of private defines needed to compile the current target. Added to the target as `PRIVATE` |
| **`INF_DEFS`** | A list of defines added to target as `INTERFACE`. As with other CMake API's, these are not used to compile the current target but added to the `INTERFACE_COMPILE_DEFINITIONS` of the exported target |
| **`INSTALL`** (`ON`\|`OFF`) | Enable installing targets. If set to `ON`, CMake will configure installation for the target (and include directories if target is a library) |

### Outputs