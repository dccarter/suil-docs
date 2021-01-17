---
title: How to Compile
weight: -18
---

{{< toc >}}

{{< hint info >}}
`SCF` thrives to be a framework that can be used to create micro service components in `C++`. To that end the framework leans towards development with docker for docker. These instructions were tested docker but should be applicable even without docker as long as dependencies are installed.
{{< /hint >}}

1. Clone the the repo
   ```sh
   git clone https://github.com/dccarter/suil.git
   ```

   {{< hint warning >}}
   This optional step opens up `suil` project in a docker container.
   ```sh
   docker run -it --rm --name suil-dev -v `pwd`/suil:/home/suil -w /home/suil suilteam/remote:ubuntu bash
   ```
   {{< /hint >}}

2. Create a build directory and run CMake from the this directory to generate the project build files
   ```sh
   mkdir build
   cd build
   cmake ..-DCMAKE_BUILD_TYPE=Debug
   ```
   The following options are available for configuring the build
   | Build option | Explanation |
   |:-------------|:------------|
   | **[`CMAKE_BUILD_TYPE`](https://cmake.org/cmake/help/latest/variable/CMAKE_BUILD_TYPE.html)** | Can be used to specified the build type (or configuration). This provided by and CMake is well documented at their website |
   | **[`CMAKE_INSTALL_PREFIX`](https://cmake.org/cmake/help/latest/variable/CMAKE_INSTALL_PREFIX.html)** | This optiona can be used to specify the installation directory that will be used when installing the build. | 
   | **`ENABLE_UNIT_TESTS`** (`ON`\|`OFF`) | If set to ON, CMake will generate unit test targets (default: `ON`) |
   | **`ENABLE_EXAMPLES`**   (`ON`\|`OFF`) | If set to ON CMake will generate targets for examples included in project (default: `ON`) |
   | **`SUIL_ENABLE_BACKTRACE`** (`ON`\|`OFF`) | Enable back tracing API. When enabled, message logged with `Critical` log level will include a backtrace (default: `OFF`) |
   | **`SUIL_ENABLE_TRACE`** (`ON`\|`OFF`) | Enable trace logging in debug builds. Trace logging is very verbose and only available in debug builds (default: `ON`) |

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

4. The project binaries can also be installed on the target system by using the `make install` command. This will install all `suil` libraries, their associated header files, generate CMake files and `scc` header files.
   ```sh
   cd suil
   mkdir -p build
   cd build
   cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=`pwd`/artifacts
   ...
   make deps
   ...
   make -j7
   ...
   make install package
   ```
   {{< hint info >}}
   - Note that I change the install prefix `-DCMAKE_INSTALL_PREFIX=$(pwd)/artifacts` to allow `make` to install artifacts to a directory named `artifacts` within my current directory.
   - Install is combined with `package` above. `suil` supports generating compressed archives of install-ables which can be  copied to another directory/system and installed without having to rebuild
   {{< /hint >}}