# avcleaner
C/C++ source obfuscator for antivirus bypass

![usage example](https://blog.scrt.ch/wp-content/uploads/2020/06/Screenshot-2020-06-18-at-17.50.54-1.png)

# Blog posts

The implementation is rather complex and this domain in software development is rarely documented in layman's terms. This is why there are blog posts which detail every design choice and go over the quirks of working with the LLVM API.

* https://blog.scrt.ch/2020/06/19/engineering-antivirus-evasion/
* https://blog.scrt.ch/2020/07/15/engineering-antivirus-evasion-part-ii/



## Build

#### tl;dr local Windows build

Prerequsites: LLVM Clang in PATH, ninja

```
cmake -B build -G Ninja
cmake --build build
```

#### Docker (untested)

```
docker build . -t avcleaner
docker run -v ~/dev/scrt/avcleaner:/home/toto -it avcleaner bash #adapt ~/dev/scrt/avcleaner to the path where you cloned avcleaner
sudo pacman -Syu
mkdir CMakeBuild && cd CMakeBuild
cmake ..
make -j 2
./avcleaner.bin --help
```

## Usage

For simple programs, this is as easy as:

```
avcleaner.bin test/strings_simplest.c --strings=true --
```

However, you should know that you're using a compiler frontend, which can only work well if you give it the path to ALL the includes required to build your project. As an example, `test/string_simplest.c` includes headers from the WinSDK, and the script `run_example.sh` shows how to handle such scenarios.

## Common errors

```
CommandLine Error: Option 'non-global-value-max-name-size' registered more than once! LLVM ERROR: inconsistency in registered CommandLine options
```

In case you encounter this error, please use `CMakeLists_archlinux.txt` instead of `CMakeLists.txt` and it should go away. 

You might also get DIA SDK error if you try to compile using CMake and Clang release:

```
ninja: error: 'C:/Program Files (x86)/Microsoft Visual Studio/2019/Professional/DIA SDK/lib/amd64/diaguids.lib', needed by 'avcleaner.bin.exe', missing and no known rule to make it
```

This is due to Clang CMake recipe hard-coding path for it.
See link for details: https://github.com/llvm/llvm-project/issues/86250
To fix, find LLVMExports.cmake in `$LLVM_LIBRARY_DIRS\cmake\llvm` (i.e. D:\a\llvm\lib\cmake\llvm). Then apply `LLVMExports.cmake.patch`.

## Contributors

Thanks @gituser5555 for the bug fixes.
