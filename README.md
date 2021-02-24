# Windows Cheminformatics Data Science Toolchain

This is a guide/tutorial/how-to aimed to help people in
building/setting-up a native "cheminformatic data science toolchain" on windows.
The aim is to guide you in getting a working and pure environment (no software bloat, no conda, no choco, ...).
Resources are important and you know that code bloat is always around the corner.

# Packages
- Visual Studio
- cmake
- python3
- freetype
- zlib
- bzip2
- boost
- eigen3
- rdkit
- tensorflow
- pytorch

Visual Studio
-------------
Download and install Visual Studio Community edition from this link https://visualstudio.microsoft.com/downloads/

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/vs_community.png)

Be sure to select the Desktop development with C++ "Workload" and in individual components
to have the latest MSVC as reported in this example:

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/vs_packages.png)

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
Compile also the library following these steps:

```
cd C:\devel\zlib-1.2.11
mkdir build
cd build
cmake ..
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
msbuild /m:12 /p:Configuration=Release ALL_BUILD.vcxproj
cp C:\devel\zlib-1.2.11\build\zconf.h C:\devel\zlib-1.2.11

mkdir C:\devel\zlib_1.2.11
mkdir C:\devel\zlib_1.2.11\include
cp C:\devel\zlib-1.2.11\build\Release\zlib.dll C:\devel\zlib_1.2.11
cp C:\devel\zlib-1.2.11\build\Release\zlib.lib C:\devel\zlib_1.2.11
cp C:\devel\zlib-1.2.11\build\Release\zlib.h C:\devel\zlib_1.2.11\include
cp C:\devel\zlib-1.2.11\build\zconf.h C:\devel\zlib_1.2.11\include

```

bzip2
-----

Download the latest bzip2 from here https://www.sourceware.org/bzip2/downloads.html
Uncompress it into your preferred directory.
In my case the directory will be C:\devel and do the following steps:

 ```
 cd C:\devel\bzip2-1.0.8
 cmd
 call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
 nmake -f makefile.msc
 mkdir c:\devel\bzip2_1.0.8
 mkdir c:\devel\bzip2_1.0.8\bin
 mkdir c:\devel\bzip2_1.0.8\include
 copy bzlib.h c:\devel\bzip2_1.0.8\include\
 copy bzlib_private.h c:\devel\bzip2_1.0.8\include
 copy libbz2.lib c:\devel\bzip2_1.0.8
 copy bzip2.exe  c:\devel\bzip2_1.0.8\bin
 copy bzip2recover.exe c:\devel\bzip2_1.0.8\bin
 ```

Freetype
--------
Download the latest freetype from here https://download.savannah.gnu.org/releases/freetype/
Uncompress and run do following steps:

```
cd C:\devel\freetype-2.10.4
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=C:\devel\freetype_2.10.4 -DZLIB_INCLUDE_DIR=C:\devel\zlib_1.2.11\include\ -DZLIB_LIBRARY=C:\devel\zlib_1.2.11\zlib.lib -DBUILD_SHARED_LIBS:BOOL=true ..
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
msbuild /m:12 /p:Configuration=Release INSTALL.vcxproj
```

N.B.: The installation path is C:\devel\freetype_2.10.4 which is different from the source path C:\devel\freetype-2.10.4.

<!---
CAIRO UNDER DEVELOPMENT
libpng
------
Download the latest libpng source from here http://www.libpng.org/pub/png/libpng.html
Uncompress the zip file into the devel directory, in my case C:\devel and do the following steps

```
cd C:\devel\lpng1637
mkdir build
cmake -DCMAKE_INSTALL_PREFIX=C:\devel\libpng-1.6.37 -DZLIB_INCLUDE_DIR=C:\devel\zlib_1.2.11\include\ -DZLIB_LIBRARY=C:\devel\zlib_1.2.11\zlib.lib ..
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
msbuild /m:12 /p:Configuration=Release INSTALL.vcxproj
```

Cairo
-----

Download  the latest Cairo source code from here https://www.cairographics.org/releases/

-->

Boost
-----

Download and install the boost 1.73.0 MSI installer from this link https://sourceforge.net/projects/boost/files/boost-binaries/1.73.0/boost_1_73_0-msvc-14.2-64.exe/download
In my case, I have installed boost on C:\devel\boost_1_73_0

Open a Windows PowerShell, go in the path where you have installed boost, enter and
compile the boost builder "b2".

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

Then in your PowerShell run the following command for boost 1.73.0
```
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64

.\b2.exe --user-config=user-config.jam address-model=64 architecture=x86 threading=multi variant=release link=shared --with-regex --disable-icu --with-thread --with-serialization --with-iostreams --with-python --with-system -sBZIP2_SOURCE="c:\devel\bzip2-1.0.8" -sZLIB_SOURCE="c:\devel\zlib-1.2.11" -sNO_LZMA=1 python-debugging=off --prefix="C:\devel\boost-1.73.0\" install
```


The configured components should appear like that

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/boost_components.png)

WARNING: Do not pay attention to the "default address-model : 32 bit". This is a bug of boost.
If you run a 64bit system and you have installed MSVC x64 will for sure compile 64 bit libraries.

When the compilation is finished, your PowerShell should appear like that.
![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/boost_completed.png)

Be sure to have in your install directory (in my case C:\devel\boost-1.73.0\lib) boost_bzip2 and  boost_zlib dll/lib.


Eigen3
------

Download the latest Eigen3 zip file from this link https://gitlab.com/libeigen/eigen/-/releases/
In my case I have downloade the version 3.3.9.
Uncompress the zip file, enter inside this directory and run the following commands:

```
mkdir build
cd build
cmake -DCMAKE_INSTALL_PREFIX=C:\devel\eigen-3.3.9  -DBOOST_ROOT=C:\devel\boost_1_73_0\ ..
```

If everything goes fine should looks like that:

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/eigen3_configuration_ok.png)

Then switch to cmd, load the environment variables and install into your path:

```
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
msbuild /m:12 /p:Configuration=Release INSTALL.vcxproj
```
If everything goes fine should looks like that:

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/eigen3_compile_ok.png)

RDKit
-----

Please download the RDKit version 2020_03_6 from this link https://github.com/rdkit/rdkit/archive/Release_2020_03_6.zip
or the version 2020_09_4 from that link https://github.com/rdkit/rdkit/archive/Release_2020_09_4.zip

Uncompress the zip archive and always with PowerShell enter inside, create a directory named build and enter inside.

```
cd .\rdkit-Release_2020_0X_X\
mkdir build
cd build
```

Then run the following command if you want to compile the 2020_03_6 or 2020_09_4:

```
cmake.exe -DBoost_NO_BOOST_CMAKE=OFF -DRDK_BUILD_PYTHON_WRAPPERS=ON -DBOOST_ROOT=C:\devel\boost-1.73.0\ -DBOOST_LIBRARYDIR=C:\devel\boost-1.73.0\lib\ -DBoost_NO_SYSTEM_PATHS=ON -DPYTHON_LIBRARY=C:\Python38\libs\python38.lib  -DPYTHON_INCLUDE_DIR=C:\Python38\include\ -DPYTHON_EXECUTABLE=C:\Python38\python.exe -DRDK_BUILD_INCHI_SUPPORT=ON -DRDK_BUILD_AVALON_SUPPORT=ON -DRDK_BUILD_MAEPARSER_SUPPORT=OFF -DRDK_INSTALL_STATIC_LIBS=OFF -DFREETYPE_LIBRARY=C:\devel\freetype_2.10.4\lib\freetype.lib -DFREETYPE_INCLUDE_DIRS=C:\devel\freetype_2.10.4\include\freetype2\ -DEIGEN3_INCLUDE_DIR=C:\devel\eigen-3.3.9\include\eigen3\ -DZLIB_LIBRARY=C:\devel\zlib_1.2.11\zlib.lib -DZLIB_INCLUDE_DIR=C:\devel\zlib_1.2.11\include\ -DCMAKE_INSTALL_PREFIX=C:\devel\rdkit-2020_09_4 -G"Visual Studio 16 2019" -A x64 ..

```

- Workaround for windows 10, MSVC 2019 and RDKit 2020_09_X

This section is applied only to RDKit version >= 2020_09_X

If you want to compile the 2020_09_4 first you need to modify this file Code/GraphMol/FileParsers/CMakeLists.txt
by commenting the line find_package(Boost ${RDK_BOOST_VERSION} COMPONENTS zlib).
The part of the CMakeLists.txt should looks like that:

```
if(RDK_USE_BOOST_IOSTREAMS)
if(WIN32)
  find_package(Boost ${RDK_BOOST_VERSION} COMPONENTS system iostreams  REQUIRED)
  # find_package(Boost ${RDK_BOOST_VERSION} COMPONENTS zlib)
  set (link_iostreams ${Boost_LIBRARIES})
  if (NOT Boost_USE_STATIC_LIBS)
     add_definitions("-DBOOST_IOSTREAMS_DYN_LINK")
  endif()
  if(Boost_zlib_FOUND)
    set(zlib_lib Boost::zlib)
  else()
    find_package(ZLIB)
    include_directories(${ZLIB_INCLUDE_DIRS})
    set(zlib_lib ${ZLIB_LIBRARIES})
  endif()
```

Modify also the file Code/GraphMol/MolDraw2D/CMakeLists.txt
and comment the line rdkit_test(moldraw2DTest1 test1.cpp LINK_LIBRARIES ...
The code part inside the CMakeLists.txt should looks like that:

```
endif(RDK_BUILD_FREETYPE_SUPPORT)

#rdkit_test(moldraw2DTest1 test1.cpp LINK_LIBRARIES
#  MolDraw2D)

rdkit_catch_test(moldraw2DTestCatch catch_main.cpp catch_tests.cpp LINK_LIBRARIES
  MolDraw2D CIPLabeler )
```


If everything goes fine should looks like that:

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/rdkit_config_ok.png)


Then switch from PowerShell to "cmd", load the MSVC environment/builder and compile your rdkit.

```
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
msbuild /m:12 /p:Configuration=Release INSTALL.vcxproj
```

If everything goes fine, the prompt should looks like that.

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/rdkit_compile_ok.png)


Then insert in your environment the RDKit paths.
Type into your "Search bar" the worlds "edit environment"

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/env_var.png)

Then add in your system environment variables
```
RDBASE = C:\devel\rdkit-Release_2020_0X_X; <<<< AS VARIABLE
C:\Python38 <<<< in PATH
C:\devel\rdkit-Release_2020_0X_X <<<< in PYTHONPATH.

Substitute X with the appropriate number!
```

Unfortunatelly, the boost and the other DLL dependencies are not loaded from the specified PATH environment variable.
https://docs.python.org/3/whatsnew/3.8.html
Then, in order to make RDKIT working DLL need to be copied manually inside the "rdkit" directory.

```
cp  C:\devel\boost-1.73.0\lib\boost_python*.dll C:\devel\rdkit-Release_2020_09_4\rdkit
cp  C:\devel\boost-1.73.0\lib\boost_iostreams*.dll C:\devel\rdkit-Release_2020_09_4\rdkit\Chem
cp  C:\devel\boost-1.73.0\lib\boost_serialization*.dll C:\devel\rdkit-Release_2020_09_4\rdkit\Chem
cp  C:\devel\boost-1.73.0\lib\boost_zlib*.dll C:\devel\rdkit-Release_2020_09_4\rdkit\Chem
cp  C:\devel\boost-1.73.0\lib\boost_bzip2*.dll C:\devel\rdkit-Release_2020_09_4\rdkit\Chem
cp  C:\devel\freetype_2.10.4\bin\freetype.dll C:\devel\rdkit-Release_2020_09_4\rdkit\Chem
cp  C:\devel\zlib_1.2.11\zlib.dll C:\devel\rdkit-Release_2020_09_4\rdkit\Chem

```


Then open a PowerShell, run python and load rdkit to see if everything is working:

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/rdkit_test_ok.png)

tensorflow
----------

For CPU
```
pip3 install tensorflow
```

For GPU
```
pip3 install tensorflow-gpu
```

pytorch
-------

Follow the official instruction at https://pytorch.org/get-started/locally/


Problems and pitfalls
---------------------

* ImportError: DLL load failed while importing rdBase

Sometimes even if you have compiled  your rdkit and boost libraries without error,
you may end-up into the following error:

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/rdkit_import_error.png)


Then to solve this just grub your boost_python library, in this case boost_python38-vc142-mt-x64-1_73.dll
and place it into the directory where rdBase.pyd is located.

This normally would solve your problem. If not, then just debug the rdBase.pyd missing libraries by
loading the Visual Studio environment variables utilised to compile rdkit and try to
see which missing libraries are needed. For that open a PowerShell, then navigate into the rdkit-Release_2020_03_6 and into rdkit
Then run this

```
cd C:\devel\rdkit-Release_2020_03_6\rdkit
cmd
call "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\VC\Auxiliary\Build\vcvarsall.bat" x64
dumpbin /dependents rdBase.pyd
```

You should see something like that, that tells you which library are missing.

![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/rdkit_debug_missing_libraries.png)

Then you can try to solve it by copying the missing libraries into the same directory C:\devel\rdkit-Release_2020_03_6\rdkit


* Eigen/Dense not found for Code\GraphMol\MolDraw2D\moldraw2DTest1.vcxproj

Sometimes you may fall into this error
![ScreenShot](https://github.com/gmrandazzo/win-cheminfds-toolchain/blob/main/images/Eigen_Dense_problems.png)

To solve it open the file Code\GraphMol\MolDraw2D\CMakeLists.txt and comment the following lines related to moldra2DTest 1.
The file should looks like that.

```
if(RDK_BUILD_CAIRO_SUPPORT)
  find_package(Cairo REQUIRED)
  target_compile_definitions(MolDraw2D PUBLIC "-DRDK_BUILD_CAIRO_SUPPORT")
  target_link_libraries(MolDraw2D PUBLIC Cairo::Cairo)
  target_sources(MolDraw2D PRIVATE MolDraw2DCairo.cpp)
  rdkit_headers(MolDraw2DCairo.h DEST GraphMol/MolDraw2D)
endif(RDK_BUILD_CAIRO_SUPPORT)

#rdkit_test(moldraw2DTest1 test1.cpp LINK_LIBRARIES
#  ChemReactions FileParsers SmilesParse Depictor RDGeometryLib
#  RDGeneral SubstructMatch Subgraphs GraphMol MolTransforms EigenSolvers
#  RDGeometryLib
#  MolDraw2D ${RDKit_THREAD_LIBS} )

rdkit_catch_test(moldraw2DTestCatch catch_tests.cpp LINK_LIBRARIES
  ChemReactions FileParsers SmilesParse Depictor RDGeometryLib
  RDGeneral SubstructMatch Subgraphs GraphMol MolTransforms EigenSolvers
  RDGeometryLib
  MolDraw2D ${RDKit_THREAD_LIBS} )
```

Then compile without parallelization

```
msbuild /p:Configuration=Release INSTALL.vcxproj
```

Links and inspiration
---------------------

The following information have been collected, reworked and prosed here to you
from multiple sources/post/links that I would like to acknowledge.

- https://gist.github.com/xiongjia/5f0c461dd4ff4984426026e9c0cb0649
- https://nielsberglund.com/2021/01/03/build-boost.python--numpy-in-windows/
- https://github.com/ZhangJunQCC/RDKit-Python36-Windows-Binary
- https://github.com/rdkit/rdkit/blob/master/Docs/Book/Install.md
- https://github.com/rdkit/rdkit/issues/2841
- https://stackoverflow.com/questions/50897959/build-a-boost-project-using-cmake-in-msvc
- https://github.com/rdkit/rdkit/pull/3714/commits/924d4acb7a2b0c54191148c4031e63ffb6b956e1
- https://stackoverflow.com/questions/20201868/importerror-dll-load-failed-the-specified-module-could-not-be-found
- https://bugs.python.org/issue36085


![Page views](https://visitor-badge.glitch.me/badge?page_id=gmrandazzo.win-cheminfds-toolchain)
