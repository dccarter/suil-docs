---
title: Tutorial
weight: -17
---
{{< toc >}}

It is really easy to develop applications against `SCF`. One either has to compile and generate artifacts from `SCF` and create an application that links against them.

# Develop With `SCF`
`SCF` provides CMake utility scripts to simplify developing applications or libraries that depend on `SCF`. All the developer needs is an `SCF` build and [`Suil Project Template`](https://github.com/dccarter/suil-template).

_**This tutorial assumes you have `SCF` binaries downloaded to `$(pwd)/scf/artifacts`**_

## Getting started with a template project
1. Clone the template repo to a directory with your desired project name
    ```sh
    git clone https://github.com/dccarter/suil-template hello-world
    cd hello-world
    rm -rf .git
    ```

2. Build the template hello-world project
    ```sh
    # Launch project in a docker container
    docker run -it --rm --name suil-dev \
        -v $(pwd)/scf/artifacts:/home/scf/artifacts \
        -v $(pwd)/hello-world:/home/scf/hello-world \
        -w /home/scf/hello-world \
        suilteam/remote:ubuntu bash
    # Build the project
    mkdir -p build
    cd build
    cmake .. -DCMAKE_BUILD_TYPE=Debug -DSUIL_BASE_PATH=/home/scf/artifacts
    make deps -j4
    make
    # Execute the built hello world app
    ./hello-world
    # Outputs-> global: Hello World! This is suil world
    ```
    

3. The template contains the following files. Note that these files are for reference only
    
    | File/Directory | Description |
    |:---------------|:------------|
    | [3rdParty.cmake](https://github.com/dccarter/suil-template/blob/main/3rdParty.cmake) | This file contains 3rdParty projects not exported within `SCF` package but needed to build dependent applications |
    | [.gitattributes](https://github.com/dccarter/suil-template/blob/main/.gitattributes) | This file is not needed to build suil, but it will help github highlight `.scc` files as `C++` files |
    | [.gitignore](https://github.com/dccarter/suil-template/blob/main/.gitignore) | Not needed for the build but it is what I use as my `.gitignore` file |
    | **[src](https://github.com/dccarter/suil-template/blob/main/src)** | This folder contains the source code of the app or library |
    | **[test](https://github.com/dccarter/suil-template/blob/main/test)** | his folder contains unit testing source code. |
    | [CMakeLists.txt](https://github.com/dccarter/suil-template/blob/main/CMakeLists.txt) | Contains the code that drives CMake. There is nothing special in this file |

### A Closer Look at `CMakeLists.txt`
The following is the listing for the [CMakeLists.txt](https://github.com/dccarter/suil-template/blob/main/CMakeLists.txt) template file. It uses `Suil.cmake` to configure an application

{{< highlight cmake "linenos=table,linenostart=2" >}}
project(hello-word LANGUAGES C CXX)
{{< /highlight >}}

This is the line that declares the name of the project. Change this to whatever you wish to name your project ([See CMake Project](https://cmake.org/cmake/help/latest/command/project.html) for more info)

{{< highlight cmake "linenos=table,linenostart=4" >}}
set(SUIL_BASE_PATH "" CACHE STRING "The root path when SCF is installed")
if (SUIL_BASE_PATH)
    set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${SUIL_BASE_PATH}/lib/cmake)
endif()
{{< /highlight >}}

Do not change this code block unless you know what you are doing. The code includes `scf` artifacts in the module lookup path for CMake. (See [`CMAKE_MODULE_PATH`](https://cmake.org/cmake/help/latest/variable/CMAKE_MODULE_PATH.html) for more info)

{{< highlight cmake "linenos=table,linenostart=9" >}}
include(Suil)
{{< /highlight >}}

This includes `Suil.cmake` from `scf` artifacts. This script provides CMake utility functions/macros used in this project.

{{< highlight cmake "linenos=table,linenostart=11" >}}
# Create a suil project
SuilProject(hello-world
        SUIL_LIBS Base)
{{< /highlight >}}

This will initialize the project declared in `line #2`. This function should only be invoked once with the name of the project.
`SuilProject` is a function provided by suil and includes most of the CMake code that would be needed to setup an `SCF` project. See [`SuilProject`](/documentation/cmake/Suil/#SuilProject) for how to use the function

{{< highlight cmake "linenos=table,linenostart=15" >}}
# Create an application target
SuilApp(hello-world
    SOURCES   src/main.cpp
    LIBRARIES Suil::Base
    INSTALL   ON)
{{< /highlight >}}

This code block will create an executable target. The parameters to given `SuilApp` are self explanatory. See [`SuilApp`](/documentation/cmake/Suil/#SuilTarget) for all the supported parameters.


# Conclusion
Using this template and `Suil.cmake` is not a requirement in developing `SCF` apps/libraries. Any developer familiar with CMake can rollout a project that depends on `suil` in a matter of minutes without using the helper script and the template.

{{< hint ok >}}
Also note that there is nothing special about the `CMakeLists.txt` included in the template. If the target project needs what's more that provided by `Suil.cmake`, any other requirements can still be added in the CMakeLists.txt.
{{< /hint >}}
**!!! Happy Coding !!!!**