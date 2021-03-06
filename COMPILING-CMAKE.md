> **WARNING**: CMake build is **NOT** official and should be used for *dev purposes ONLY*. Official build instructions (using `make`) are documented in [COMPILING.md](./COMPILING.md).

# Contents

1. [Prerequisites](#prerequisites)
1. [Build environment](#environment)
	* [UNIX environment](#unix-environment)
	* [Windows environment (MSYS2)](#msys-environment)
1. [CMake build](#build)
	* [MSYS + MinGW](#build-msys)
	* [Visual Studio / MSBuild](#build-vstudio-msbuild)
1. [Build options](#build-options)
	* [CMake-specific](#cmake-build-options)
	* [Cataclysm-specific](#cataclysm-build-options)

<a id="prerequisites"></a>
# Prerequisites

You'll need to have these libraries and their development headers installed in order to build Cataclysm-DDA:

* General
	* `cmake`     >= 3.0.0
	* `gcc-libs`
	* `glibc`
	* `zlib`
	* `bzip2`
* Localization
	* `gettext`
* Curses
	* `ncurses`
* Tiles
	* `SDL`       >= 2.0.0
	* `SDL_image` >= 2.0.0 (with PNG and JPEG support)
	* `SDL_mixer` >= 2.0.0 (with Ogg Vorbis support)
	* `SDL_ttf`   >= 2.0.0
	* `freetype`
* Sound
	* `vorbis`
	* `libbz2`
	* `libz`
	* `libintl`
	* `iconv`


<a id="environment"></a>
# Build environment

You can obtain the source tree from the remote repository.

```
$ git clone https://github.com/CleverRaven/Cataclysm-DDA.git
```

This will fetch the latest development branch. In addition, if you want a specific release, you can list available tags and checkout.

```
$ git tag --list
...
0.A
0.B
0.C
0.D
...

$ git checkout tags/0.D
```

<a id="unix-environment"></a>
## UNIX environment

Obtain packages specified above with your system package manager.

<a id="msys-environment"></a>
## Windows environment (MSYS2)

1. Follow steps from here: https://msys2.github.io/
1. Install Cataclysm-DDA build deps:

```
$ pacman -S mingw-w64-i686-toolchain msys/git \
            mingw-w64-i686-cmake              \
            mingw-w64-i686-SDL2_image         \
            mingw-w64-i686-SDL2_mixer         \
            mingw-w64-i686-SDL2_ttf           \
            ncurses-devel                     \
            gettext-devel
```

This should get your environment set up to build console and tiles version of windows.

> **NOTE**: This is only for 32 bit builds. 64 bit requires the x86_64 instead of the i686 packages listed above:

```
$ pacman -S mingw-w64-x86_64-toolchain msys/git   \
            mingw-w64-x86_64-cmake                \
            mingw-w64-x86_64-SDL2_image,mixer,ttf \
            mingw-w64-x86_64-SDL2_mixer           \
            mingw-w64-x86_64-SDL2_ttf             \
            ncurses-devel                         \
            gettext-devel
```

> **NOTE**: If you're trying to test with Jetbrains CLion, point to the cmake version in the msys32/mingw32 path instead of using the built in. This will let cmake detect the installed packages.

<a id="cmake-build"></a>
# CMake build

CMake has separate configuration and build steps. Configuration is done using CMake itself, and the actual build is done using either `make` (for Makefiles generator) or build-system agnostic `cmake --build . `.

There are two ways to build Cataclysm-DDA with CMake: inside the source tree or outside of it. Out-of-source builds have the advantage that you can have multiple builds with different options from one source directory.

> **WARNING**: Inside the source tree build is **NOT** supported.

To build Cataclysm-DDA out of the source tree:

```
$ mkdir build && cd build
$ cmake .. -DCMAKE_BUILD_TYPE=Release
$ make
```

The above example creates a build directory inside the source directory, but that's not required - you can just as easily create it in a completely different location.

To install Cataclysm-DDA after building:

```
# make install
```

To change build options, you can either pass the options on the command line:

```
$ cmake .. -DOPTION_NAME=option_value
```

Or use either the `ccmake` or `cmake-gui` front-ends, which display all options and their cached values on a console and graphical UI, respectively.

```
$ ccmake ..
$ cmake-gui ..
```

<a id="build-msys"></a>
## MSYS + MinGW

> **NOTE**: For development purposes it is preferred to use `MinGW Win64 Shell` or `MinGW Win32 Shell` instead of `MSYS2 Shell`. In other case, you will need to set `PATH` variable manually.

For MinGW, MSYS, MSYS2 you should set [Makefiles generator](https://cmake.org/cmake/help/v3.0/manual/cmake-generators.7.html) to "MSYS Makefiles". Setting it to "MinGW Makefiles" might work as well, but might also require additional hackery.

Example:

```
$ cd <Path-to-Cataclysm-DDA-Sources>
$ mkdir build
$ cd build
$ cmake .. -G "MSYS Makefiles"
$ cmake --build .
```

The resulting binary will be placed inside the source code directory.

Shared libraries:

If you got `libgcc_s_dw2-1.dll not found` error you need to copy shared libraries to directory with Cataclysm-DDA executables.

> **NOTE**: For `-DRELEASE=OFF` development builds, You can automate copy process with:

```
# make install
```

However, it likely will fail b/c you have different build environment setup :)

Currently known depends (Maybe outdated use ldd.exe to correct it for Your system)

* MINGW deps:
	* `libwinpthread-1.dll`
	* `libgcc_s_dw2-1.dll`
	* `libstdc++-6.dll`

* LOCALIZE deps:
	* `libintl-8.dll`
	* `libiconv-2.dll`

* TILES deps:
	* `SDL2.dll`
	* `SDL2_ttf.dll`
	* `libfreetype-6.dll`
	* `libbz2-1.dll`
	* `libharfbuzz-0.dll`
	* `SDL2_image.dll`
	* `libpng16-16.dll`
	* `libjpeg-8.dll`
	* `libtiff-5.dll`
	* `libjbig-0.dll`
	* `liblzma-5.dll`
	* `libwebp-5.dll`
	* `zlib1.dll`
	* `libglib-2.0-0.dll`

* SOUND deps:
	* `SDL2_mixer.dll`
	* `libFLAC-8.dll`
	* `libogg-0.dll`
	* `libfluidsynth-1.dll`
	* `libportaudio-2.dll`
	* `libsndfile-1.dll`
	* `libvorbis-0.dll`
	* `libvorbisenc-2.dll`
	* `libmodplug-1.dll`
	* `smpeg2.dll`
	* `libvorbisfile-3.dll`


<a id="build-vstudio-msbuild"></a>
## Visual Studio / MSBuild

CMake can generate  `.sln` and `.vcxproj` files used either by Visual Studio itself or by MSBuild command line compiler (if you don't want a full fledged IDE) and have more "native" binaries than what MSYS/Cygwin can provide.

At the moment only a limited combination of options is supported (tiles only, no localizations, no backtrace).

Get the tools:

* CMake from the official site - https://cmake.org/download/.
* Microsoft compiler - https://visualstudio.microsoft.com/downloads/?q=build+tools , choose "Build Tools for Visual Studio 2017". When installing chose "Visual C++ Build Tools" options.
	* alternatively, you can get download and install the complete Visual Studio, but that's not required.

Get the required libraries:

* `SDL2` - https://www.libsdl.org/download-2.0.php (you need the "(Visual C++ 32/64-bit)" version. Same below)
* `SDL2_ttf` - https://www.libsdl.org/projects/SDL_ttf/
* `SDL2_image` - https://www.libsdl.org/projects/SDL_image/
* `SDL2_mixer` (optional, for sound support) - https://www.libsdl.org/projects/SDL_mixer/
* Unsupported (and unused in the following instructions) optional libs:
	* `gettext`/`libintl` - http://gnuwin32.sourceforge.net/packages/gettext.htm
	* `ncurses` - ???

Unpack the archives with the libraries.

Open windows command line (or powershell), set the environment variables to point to the libs above as follows (adjusting the paths as appropriate):

```
> set SDL2DIR=C:\path\to\SDL2-devel-2.0.9-VC
> set SDL2TTFDIR=C:\path\to\SDL2_ttf-devel-2.0.15-VC
> set SDL2IMAGEDIR=C:\path\to\SDL2_image-devel-2.0.4-VC
> set SDL2MIXERDIR=C:\path\to\SDL2_mixer-devel-2.0.4-VC
```

(for powershell the syntax is `$env:SDL2DIR="C:\path\to\SDL2-devel-2.0.9-VC"`).

Make a build directory and run cmake configuration step:

```
> cd <path to cdda sources>
> mkdir build
> cd build
> cmake .. -DTILES=ON -DLOCALIZE=OFF -DBACKTRACE=OFF -DSOUND=ON
```

Build!

```
> cmake --build . -j 2 -- /p:Configuration=Release
```

> The `-j 2` flag controls build parallelism - you can omit it if you wish. The `/p:Configuration=Release` flag is passed directly to MSBuild and controls optimizations. If you omit it, the `Debug` configuration would be built instead.  For powershell you'll need to have an extra ` -- ` after the first one.

The resulting files will be put into a `Release` directory inside your source Cataclysm-DDA folder. To make them run you'd need to first move them to the source Cataclysm-DDA directory itself (so that the binary has access to the game data), and second put the required `.dll`s into the same folder - you can find those inside the directories for dev libraries under `lib/x86/` or `lib/x64/` (you likely need the `x86` ones even if you're on 64-bit machine).

The copying of dlls is a one-time task, but you'd need to move the binary out of `Release/` each time it's built. To automate it a bit, you can configure cmake and set the desired binaries destination directory with `-DCMAKE_RUNTIME_OUTPUT_DIRECTORY_RELEASE=`  option (and similar for `CMAKE_RUNTIME_OUTPUT_DIRECTORY_DEBUG`).

<a id="build-options"></a>
# Build options

A full list of options supported by CMake, you may either run the `ccmake` or `cmake-gui` front-ends, or run `cmake` and open the generated `CMakeCache.txt` from the build directory in a text editor.

```
$ cmake -DOPTION_NAME1=option_value1 [-DOPTION_NAME2=option_value2 [...]]
```

<a id="cmake-build-options"></a>
## CMake-specific

* `CMAKE_BUILD_TYPE=<build type>`

Selects a specific build configuration when compiling. `release` produces  the default, optimized (-Os) build for regular use. `debug` produces a  slower and larger unoptimized (-O0) build with full debug symbols, which is  often needed for obtaining detailed backtraces when reporting bugs.

> **NOTE**: By default, CMake will produce `debug` builds unless a different configuration option is passed in the command line.

* `CMAKE_INSTALL_PREFIX=<full path>`

Installation prefix for binaries, resources, and documentation files.

<a id="cataclysm-build-options"></a>
## Cataclysm-specific

* `CURSES=<boolean>`

Build curses version.

* `TILES=<boolean>`

Build graphical tileset version.

* `SOUND=<boolean>`

Support for in-game sounds & music.

* `USE_HOME_DIR=<boolean>`

Use user's home directory for save files.

* `LOCALIZE=<boolean>`

Support for language localizations. Also enable UTF support.

* `DYNAMIC_LINKING=<boolean>`

Use dynamic linking. Or use static to remove MinGW dependency instead.

* `LANGUAGES=<str>`

Compile localization files for specified languages. Example:

```
-DLANGUAGES="cs;de;el;es_AR;es_ES"
```

* `GIT_BINARY=<str>`

Override default Git binary name or path.

So a CMake command for building Cataclysm-DDA in release mode with tiles and sound support will look as follows, provided it is run in build directory located in the project.

```
$ cmake ../ -DCMAKE_BUILD_TYPE=Release -DTILES=ON -DSOUND=ON
```
