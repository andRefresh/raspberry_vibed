Raspberry Pi vibe.d tutorial
============================
A tutorial/template to build a vibed web application on a raspberryPi board.
The board can be used as a REST server to use the Pi IO with the `dgpio` dub package.

*NOTE:* The project has been tested on ArchLinuxARM on a RaspberryPi3 board compiled from an Ubuntu 18.04 x86-64 environment.

Why this?
---------

One can use the flask-python stack to use the the Pi as a REST IOT server.
But D is really __FAST__ on the pi board. No jokes.


How to build
------------
Set the variables in the `ldc-pi` script.
Remember that the path directories must be absolute and not relative in order to avoid weird situations within `dub`.
Then:

```sh
chmod +x ./ldc-pi
dub --compiler=./ldc-pi
```

ldc-pi is a shell script to set the local variables. This is necessary to propagate the build settings to every project dependency.
The important flags used are the same for the LDC Runtime cross-building:
`ldc2 -mtriple=arm-linux-gnueabihf -gcc=arm-linux-gnueabihf-gcc`

How to cross compile the dependencies
-------------------------------------
Make sure to have installed the cross compiler `arm-linux-gnueabihf-gcc`.
Open a terminal and digit:

```sh
sudo apt-get install gcc-arm-linux-gnueabihf
```

LDC Runtime
-----------
Follow the instructions here: [Building_LDC_runtime_libraries](https://wiki.dlang.org/Building_LDC_runtime_libraries).
Again: make sure to have the dependencies required

```sh
sudo apt-get install gcc-arm-linux-gnueabihf ninja-build cmake
```

Be sure to have the last LDC2 installed on the system.
You can find them here: [LDC2_Releases](https://github.com/ldc-developers/ldc/releases/).
Download the one that fits the host machine. For example if you are compiling the project on a Intel processor you need to download the `linux-x86_64` one.

After that:
```sh
export LDC2_DIR=/path/to/ldc2
export CC=arm-linux-gnueabihf-gcc
$LDC2_DIR/bin/ldc-build-runtime --ninja --dFlags="-w;-mtriple=arm-linux-gnueabihf"
```
This will build the `ldc-build-runtime.tmp` folder you can link at after at build time.

Vibe.d has two external dependencies that need a manual cross compilation (or a properly setted cross-gcc toolchain):

OpenSSL
--------

```sh
git clone https://github.com/openssl/openssl
```

As done for LDC Runtime let's cross-compile the openssl library.
Enter the directory and set the `./Configure` script variables.

```sh
export INSTALL-DIR=/path/to/openssl/install/dir

./Configure linux-generic32 shared \
--prefix=$INSTALL_DIR --openssldir=$INSTALL_DIR/openssl \
--cross-compile-prefix=arm-linux-gnueabihf-
```
*Note*: look at the cross compile prefix variabile. It will append `gcc` automatically. It must use the same compiler used for the ldc runtime.

Then finally:

```sh
make
make install
```
The folder user for the `INSTALL-DIR` now contains the libraries cross-compiled. You can see an example in the `lib` folder.

ZLIB
----

Finally let's cross compile the *ZLIB* library.
Download it from the [ZLIB_Website](https://zlib.net).
As done for the open-ssl:
```sh
export CC=arm-linux-gnueabihf-gcc
export INSTALL-DIR=/path/to/zlib/install/dir
make
make install
```
And we are done.


Web references
--------------
- [Programming_in_D_tutorial_on_Embedded_Linux_ARM_devices](https://wiki.dlang.org/Programming_in_D_tutorial_on_Embedded_Linux_ARM_devices)
- [LDC cross-compilation for ARM GNU/Linux](https://wiki.dlang.org/LDC_cross-compilation_for_ARM_GNU/Linux)

