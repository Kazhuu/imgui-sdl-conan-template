# imgui-sdl-template

IMGUI + SDL2 + OpenGL 3 project template with CMake and Conan package
manager for Windows and Linux machines.

This template is based on
[this IMGUI example](https://github.com/ocornut/imgui/tree/master/examples/example_sdl_opengl3).

## Prerequisites

After cloning this repository you need to install IMGUI with Conan. First
install [Conan](https://conan.io/) for your system and make sure `conan` command
is working for you.

You need to install SDL2 on your system. On Arch Linux for example

```shell
sudo pacman -S sdl sdl2
```

And on Windows MSYS2/MinGW do

```shell
pacman -S mingw-w64-i686-SDL2
```

You system also need to have OpenGL drivers installed for your GPU. On Linux
[Mesa](https://mesa3d.org/) for example. Check your systems package manager for
it.

## Notes

Key part of making IMGUI to compile with Conan is `[imports]` section in
`conanfile.txt` file. This will copy IMGUI backend implementation files to
`./src/bindings/` folder during the install process. Then this folder is used
normally by CMake. You can read more about Conan imports
[here](https://docs.conan.io/en/latest/using_packages/conanfile_txt.html#imports-txt).
The path `.res/bindings` path in `[imports]` section matches the built package
under Conan folder. For me for my Arch machine the IMGUI package path is
`/home/kazhuu/.conan/data/imgui/1.89.1/_/_/package/174df609440df17baf334e1f5953cca909f2ae18`.

## Build

### Windows MSYS2

If you are using Windows and MSYS2. Make sure you install MinGW version of CMake
`mingw-w64-x86_64-cmake`. This will ensure that you are building for Windows.
You also need to install the tool for actual building. By default Ninja is used
if generator for the CMake is not specified. Intall Ninja from
`mingw-w64-x86_64-ninja`. For futher information check
[CMake forMSYS2](https://www.msys2.org/docs/cmake/).

Install needed packages with

```
pacman -S mingw-w64-x86_64-cmake mingw-w64-x86_64-ninja
```

Then just run following to build. If you want release build add
`-DCMAKE_BUILD_TYPE=Release` to first CMake command.

```shell
mkdir build && cd build
cmake ..
cmake --build .
```

### Linux

To build run following commands. If you want release build add
`-DCMAKE_BUILD_TYPE=Release` to first CMake command. Notice that
`--build=missing` flag with Conan. This is to tell Conan to compile sources
locally if fetching prebuilt binaries fail.

```shell
mkdir build && cd build
conan install .. --build=missing
cmake ..
cmake --build .
```

## Running

After everything compiles, executable `imgui_example` will be placed under your
`build/bin` directory. To run it in the build folder

```shell
./bin/imgui_example
```
