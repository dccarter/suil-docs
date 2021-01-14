---
title: Requirements
weight: -20
---

{{< toc >}}

# Environment
`SCF` currently targets Linux environments such as Ubuntu and Alpine. It has been tested on Alpine and Ubuntu `x86_64` architecture. Support for other architectures hasn't been tested, but supporting such architectures will require adding/verifying support for them on [Suil/Libmill](https://gitlab.com/sw-devel/thirdparty/libmill).
- **Linux OS**
- **`C++2a`** the build is continuously tested with `gcc-10.2` but any other `gcc` compiler version that supports `C++2a` should work, otherwise the effort to make it work will be minimal
- Target compilation CPU should be **`x86_64`**. Other target's haven't been tested

# Dependencies
- **Build Essential**
- **CMake** CMake is used to generate targets of this project. I have only ever target Unix Makefiles generator, users are welcome to try other targets possible on Linux & GCC and provide feedback accordingly.
## Required Libraries
The following libraries presented in an typical `apt` package install command are required for development on Ubuntu or similar OS.
```sh
apt-get update
apt-get install sqlite3 libsqlite3-dev libpq-dev postgresql \
                postgresql-server-dev-all uuid-dev openssl1.0 libssl1.0-dev \
                libzmq3-dev git
```
**[bitcoin-core/secp256k1](https://github.com/bitcoin-core/secp256k1.git)** is also required to build the crypto module. Follow installation instructions from the linked GitHub repo

{{< hint info >}}
This dependencies are just a hint on tested environments. Different environments might require different tool chains or libraries. Leaving that up to the developer to figure out.
That being said, development on Linux has been simplified by providing docker images that can readily build this project. See [Develop With Docker](/getting-started/develop-with-docker) for more information on development options with docker
{{< /hint >}}