# WINCDST: Windows Cheminformatic Data Science Toolchain

This is a guide/tutorial/how-to aimed to help people in
building/setting-up a "cheminformatic data science toolchain" for windows.
It will describe the packages to install/compile in sequence and
will guide you in getting a working and pure environment (no bloat, no conda, no choco, ...).
Resources are important. Code bloat is always around the corner.

# Packages
- Visual Studio
- cmake
- python3
- zlib
- bzip2
- boost
- rdkit
- tensorflow
- pytorch

Visual Studio
-------------
Download and install Visual Studio Community edition from this link https://visualstudio.microsoft.com/downloads/

![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/vs_community.png)

Be sure to select the Desktop development with C++ "Workload" and in individual components
to have the latest MSVC as reported in this example:

![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/vs_packages.png)

CMake
-----

Download and install CMake 3.18.6 from this link https://github.com/Kitware/CMake/releases/tag/v3.18.6

This version is important because allows to build later RDKit with boost version 1.73.
In my case I download cmake-3.18.6-win64-x64.msi because I'm running windows 10 64bit.
Choose your appropriate case.

Python3
-------

Download and install Python3.8 from this link https://www.python.org/ftp/python/3.8.7/python-3.8.7-amd64.exe
This version is important because currently the latest tensorflow version is only supported for versions 3.6-3.8

Install it in your preferred directory. In my case will be C:\devel

zlib
----

Download the latest zlib from here https://www.zlib.net/ and uncompress it into your preferred directory.
In my case this directory will be C:\devel\zlib-1.2.11

bzip2
-----

Download the latest bzip2 from here https://www.sourceware.org/bzip2/downloads.html and uncompress it into your preferred directory.
In my case this directory will be C:\devel\bzip2-1.0.8

Boost
-----

Download and install the boost 1.73.0 MSI installer from this link https://sourceforge.net/projects/boost/files/boost-binaries/1.73.0/boost_1_73_0-msvc-14.2-64.exe/download
In my case, I have installed boost on C:\devel\boost_1_73_0

Open a Windows PowerShell, go in the path where you have uncompressed boost, enter inside it and
compile the boost builder.

```
cd c:\devel\boost_1_73_0
./bootstrap.bat --without-icu
```

Create user-config.jam as in this example.
Please substitute c:\Python38\ with your python path.
In my case is always in c:\devel

```
using python
    : 3.8                   # Version
    : C:\\Python38\\python.exe      # Python Path
    : C:\\Python38\\Include         # include path
    : C:\\Python38\\libs            # lib path(s)
    ;
  ```

Then in your PowerShell run the following command

```
.\b2.exe --user-config=user-config.jam address-model=64 architecture=x86 threading=multi variant=release link=shared --with-regex --disable-icu --with-thread --with-serialization --with-iostreams --with-python --with-system -sBZIP2_SOURCE="c\devel\bzip2-1.0.8" -sZLIB_SOURCE="c:\devel\zlib-1.2.11" -sNO_LZMA=1 python-debugging=off install
```


The configured components should appear like that

![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/boost_components.png)

WARNING: Do not pay attention to the "default address-model : 32 bit". This is a bug of boost.
If you run a 64bit system and you have installed MSVC x64 will for sure compile 64 bit libraries.

When the compilation is finished, your PowerShell should appear like that.
![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/boost_completed.png)


RDKit
-----

Please download the RDKit version 2020_03_6 from this link https://github.com/rdkit/rdkit/archive/Release_2020_03_6.zip
For the moment it works without problems with MSVC 2019 and boost 1.73 only this version.
I'm working in making also working newer versions and there are still some bugs to solve.

Uncompress the zip archive and always with PowerShell enter inside, create a directory named build and enter inside.
```
cd .\rdkit-Release_2020_03_6\
mkdir build
cd build
```

Then run the following command:

```
cmake.exe -DBoost_NO_BOOST_CMAKE=OFF -DRDK_BUILD_PYTHON_WRAPPERS=ON -DBOOST_ROOT=C:\devel\boost_1_73_0\ -DBOOST_LIBRARYDIR=C:\devel\boost_1_73_0\lib\ -DBoost_NO_SYSTEM_PATHS=ON -DPYTHON_LIBRARY=C:\Python38\libs\python38.lib  -DPYTHON_INCLUDE_DIR=C:\Python38\include\ -DPYTHON_EXECUTABLE=C:\Python38\python.exe -DRDK_BUILD_INCHI_SUPPORT=ON -DRDK_BUILD_AVALON_SUPPORT=ON -G"Visual Studio 16 2019" -A x64 ..
```

If everything goes fine should looks like that:

![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/rdkit_config_ok.png)

Then switch from PowerShell to "cmd", load the MSVC environment/builder and compile your rdkit.

```
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
msbuild /m:12 /p:Configuration=Release INSTALL.vcxproj
```

If everything goes fine, the prompt should looks like that.

![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/rdkit_compile_ok.png)


Then insert in your environment the RDKit paths.
Type into your "Search bar" the worlds "edit environment"

![ScreenShot](https://github.com/gmrandazzo/WINCDST/blob/master/images/env_var.png)

Then add in your system environment variables

RDBASE = C:\RDKit; << Variable
C:\Python36 in PATH;
C:\devel\rdkit-Release_2020_03_6\lib in PATH;
C:\devel\boost_1_73_0\lib in PATH;
C:\devel\rdkit-Release_2020_03_6 in PYTHONPATH.



Links and inspiration
---------------------

The following information have been collected, reworked and prosed here to you
from multiple sources/post/links that I would like to acknowledge.

https://nielsberglund.com/2021/01/03/build-boost.python--numpy-in-windows/
https://github.com/ZhangJunQCC/RDKit-Python36-Windows-Binary
https://github.com/rdkit/rdkit/blob/master/Docs/Book/Install.md
https://github.com/rdkit/rdkit/issues/2841
