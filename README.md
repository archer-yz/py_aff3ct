# Python to AFF3CT (Supports Windows Platform)

The original [py_aff3ct](https://github.com/aff3ct/py_aff3ct) repository does not support Windows
platform yet. Some modifications are made in this fork to support Windows and MSVC.

## Get Libraries and `doxygen`

Get all libraries:

```bash
git clone --recurse-submodules <github url>
```

Download `doxygen` from https://www.doxygen.nl/download.html.

## Compile `AFF3CT`

**Set C++ standard to 17**

Go into `aff3ct` folder:

```bash
$ cd lib/aff3ct
```

Edit the `CMakeLists.txt` line for C++ standard:

```cmake
set(CMAKE_CXX_STANDARD 17)
```

Edit the `CMakeLists.txt` in `streampu` submodule to turn off `SPU_STACKTRACE`:

```cmake
option(SPU_STACKTRACE          "Print the stack trace when an exception is raised"           OFF )
```

**Compile `aff3ct` on Windows & MSVC:**

```bash
$ mkdir build
$ cd build
$ cmake .. -G "Visual Studio 17 2022" -A x64 -DAFF3CT_COMPILE_EXE="OFF" -DAFF3CT_COMPILE_STATIC_LIB="ON" -DAFF3CT_COMPILE_SHARED_LIB="ON"
$ cmake --build . --config Release
```

The `AFF3CT` static and dynamic libraries are built in the `lib/aff3ct/build/release` folder.

> [!note]
> C++ standard 17 should be used for compilation, otherwise there will be issues for wrapping LDPC decoding modules..

**Compile the AFF3CT documentation with `Doxygen`:**

```bash
$ cd ../doc
$ mkdir build
$ cd source
$ doxygen Doxyfile
```

> [!note]
> Generated XML document will be used for creating Python wrappers.

## Compile `py_aff3ct`

**Go back to the `py_aff3ct` project folder.**

```bash
$ cd ../../../../
```

**Copy the CMake configuration files from the `AFF3CT` build**

```bash
$ for /D %D in ("lib\aff3ct\build\lib\cmake\aff3ct-*") do robocopy "%D" "cmake\Modules" /E
```

**Compile `py_aff3ct`**

```bash
$ mkdir build
$ cd build
$ python ../configure.py --verbose
$ cmake .. -G "Visual Studio 17 2022" -A x64
$ cmake --build . --config Release
```

The compiled library is in `/build/lib/Release` folder.

---

> [!note]
> The followings are the **README** from original repository.

# Python to AFF3CT

This repository contains a code example for integrating a `Python` module inside
an AFF3CT sequence.

## Using AFF3CT as a library for your codes

The first step is to compile AFF3CT into a library.

Prerequisite:

	$ sudo apt install python3 python3-pip doxygen

Get the AFF3CT library:

	$ git submodule update --init --recursive

Compile the library on Linux/MacOS/MinGW:

	$ cd lib/aff3ct
	$ mkdir build
	$ cd build
	$ cmake .. -G"Unix Makefiles" -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-funroll-loops -march=native -fvisibility=hidden -fvisibility-inlines-hidden -faligned-new" -DAFF3CT_COMPILE_EXE="OFF" -DAFF3CT_COMPILE_STATIC_LIB="ON" -DAFF3CT_COMPILE_SHARED_LIB="ON"
	$ make -j4

Now the AFF3CT library has been built in the `lib/aff3ct/build` folder.

Compile the documentation with `Doxygen`:

	$ cd ../doc
	$ mkdir build
	$ cd source
	$ doxygen Doxyfile

Go back to the project root:

	$ cd ../../../../

## Compile your code

Copy the `CMake` configuration files from the AFF3CT build

	$ mkdir cmake && mkdir cmake/Modules
	$ cp lib/aff3ct/build/lib/cmake/aff3ct-*/* cmake/Modules

Compile the code on Linux/MacOS/MinGW:

	$ mkdir build && cd build
	$ ../configure.py --verbose
	$ cmake .. -G"Unix Makefiles" -DCMAKE_BUILD_TYPE=Release -DCMAKE_CXX_FLAGS="-Wall -funroll-loops -march=native -fvisibility=hidden -fvisibility-inlines-hidden -faligned-new"
	$ make -j4

The compiled library is in `build/lib/py_aff3ct*.so`.

Check if it works:

	$ cd ../examples/full_python/
	$ python3 test.py
