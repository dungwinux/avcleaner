cmake_minimum_required(VERSION 3.6)
project(avcleaner LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

if (WIN32)
        # MultiThreaded
        # Compile with -MT or equivalent flag(s) to use a multi-threaded statically-linked runtime library.
        #
        # MultiThreadedDLL
        # Compile with -MD or equivalent flag(s) to use a multi-threaded dynamically-linked runtime library.
        #
        # MultiThreadedDebug
        # Compile with -MTd or equivalent flag(s) to use a multi-threaded statically-linked runtime library.
        #
        # MultiThreadedDebugDLL
        # Compile with -MDd or equivalent flag(s) to use a multi-threaded dynamically-linked runtime library.
        # You must link with compatible library that your clang binary uses
        # Or if you compile yourself, you may have to look the other way
        set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded")
        # TODO: Check if CMAKE_BUILD_TYPE if the same as LLVM build type

        # Removing these because it messes up Runtime library linking and conflicts with the above setting
        # -- Build flag: -O0 -g -Xclang -gcodeview -D_DEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrtd
        # -- Build flag: -O3 -DNDEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrt
        # -- Build flag: -Os -DNDEBUG -D_DLL -D_MT -Xclang --dependent-lib=msvcrt
        # -- Build flag: -O2 -DNDEBUG -g -Xclang -gcodeview -D_DLL -D_MT -Xclang --dependent-lib=msvcrt
        set(CMAKE_CXX_FLAGS_DEBUG "")
        set(CMAKE_CXX_FLAGS_RELEASE "")
        set(CMAKE_CXX_FLAGS_MINSIZEREL "")
        set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "")
endif()

#set(CMAKE_FIND_LIBRARY_SUFFIXES .a ${CMAKE_FIND_LIBRARY_SUFFIXES})
#SET(CMAKE_EXE_LINKER_FLAGS $(llvm-config --ldflags))

# list(APPEND CMAKE_CXX_SOURCE_FILE_EXTENSIONS c)

find_package(Clang REQUIRED CONFIG)
message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")
message(STATUS "Using ClangConfig.cmake in: ${CLANG_CMAKE_DIR}")
message(STATUS "LLVM Include ${LLVM_INCLUDE_DIRS}")
message(STATUS "LLVM Link ${LLVM_LIBRARY_DIRS}")

# HandleLLVMOptions sets up compilation flags (disable warnings, etc.)
list(APPEND CMAKE_MODULE_PATH ${LLVM_DIR})
include(HandleLLVMOptions)

add_executable(avcleaner.bin
        main.cpp
        MatchHandler.cpp
        Globals.cpp
        ApiMatchHandler.cpp ApiMatchHandler.h Utils.cpp Utils.h)

include_directories(${LLVM_INCLUDE_DIRS})
link_directories(${LLVM_LIBRARY_DIRS})
# add_definitions(${LLVM_DEFINITIONS})
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-unused-parameter -fno-strict-aliasing -fno-rtti")

#target_link_libraries(avcleaner.bin LLVM clangTooling clangToolingInclusions clangLex clangToolingCore clangAST clangASTMatchers clangBasic clangFrontend clangFrontendTool clangRewrite clangRewriteFrontend clangSerialization

target_link_libraries(avcleaner.bin LLVMSupport clangTooling)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
