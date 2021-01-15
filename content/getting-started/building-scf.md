---
title: Compiling
weight: -18
---

{{< toc >}}

CMake can be using to generate project files

1. Clone the the repo
   ```sh
   git clone https://github.com/dccarter/suil.git
   ```

2. Create a build directory and run CMake from the this directory to generate the project build files
   ```sh
   mkdir build
   cd build
   cmake ..-DCMAKE_BUILD_TYPE=Debug
   ```
   The following options are available for configuring the build
   - **[`CMAKE_BUILD_TYPE`](https://cmake.org/cmake/help/latest/variable/CMAKE_BUILD_TYPE.html)** can be used to specified the build type (or configuration). This provided by and CMake is well documented at that link
   - **[`CMAKE_INSTALL_PREFIX`](https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_PREFIX.html)** can be used to specify the installation directory that will be used when installing the build.
   - **`ENABLE_UNIT_TESTS`** (`ON`|`OFF`) if set to ON, CMake will generate unit test targets (default: `ON`)
   - **`ENABLE_EXAMPLES`**   (`ON`|`OFF`) if set to ON CMake will generate targets for examples included in project (default: `ON`)
   - **`SUIL_ENABLE_BACKTRACE`** (`ON`|`OFF`) Enable back tracing API. When enabled, message logged with `Critical` log level will include a backtrace (default: `OFF`)
   - **`SUIL_ENABLE_BACKTRACE`** (`ON`|`OFF`) Enable trace logging in debug builds. Trace logging is very verbose and only available in debug builds (default: `ON`)

2. After successfully building a project, third party dependencies need to be built before attempting to build any other component.
   ```sh
   make deps
   ```
   _This step needs to be run when 3rdParty dependencies have been updated to pull the updates._

3. Build the project with the following command (if your machine has the power to parallelize make, please do so by appending `-jN` to the command where `N` is the desired parallelism)
   ```sh
   make
   ```
   This command will build suil libraries, `scc` generator plugins , unit tests and examples (if enabled). 