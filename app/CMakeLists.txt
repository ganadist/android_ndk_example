# For more information about using CMake with Android Studio, read the
# documentation: https://d.android.com/studio/projects/add-native-code.html

# Sets the minimum version of CMake required to build the native library.

cmake_minimum_required(VERSION 3.4.1)


# Variables
set(SOURCE_ROOT src/main/cpp/Source/C++)
set(SOURCE_CODECS ${SOURCE_ROOT}/Codecs)
set(SOURCE_CORE ${SOURCE_ROOT}/Core)
set(SOURCE_CRYPTO ${SOURCE_ROOT}/Crypto)
set(SOURCE_METADATA ${SOURCE_ROOT}/MetaData)
set(SOURCE_SYSTEM ${SOURCE_ROOT}/System)

# Compiler warning and optimization flags
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall")

# workaround
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DAP4_PLATFORM_BYTE_ORDER=AP4_PLATFORM_BYTE_ORDER_LITTLE_ENDIAN")

if (EMSCRIPTEN)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-warn-absolute-paths")
endif()

# AP4 Library
file(GLOB AP4_SOURCES
  ${SOURCE_CODECS}/*.cpp
  ${SOURCE_CORE}/*.cpp
  ${SOURCE_CRYPTO}/*.cpp
  ${SOURCE_METADATA}/*.cpp
  ${SOURCE_SYSTEM}/StdC/*.cpp
)

if(WIN32)
  set(AP4_SOURCES ${AP4_SOURCES} ${SOURCE_SYSTEM}/Win32/Ap4Win32Random.cpp)
else()
  set(AP4_SOURCES ${AP4_SOURCES} ${SOURCE_SYSTEM}/Posix/Ap4PosixRandom.cpp)
endif()

add_library(ap4 STATIC ${AP4_SOURCES})

# Includes
include_directories(
  ${SOURCE_CORE}
  ${SOURCE_CODECS}
  ${SOURCE_CRYPTO}
  ${SOURCE_METADATA}
)

set(EXECUTABLE_OUTPUT_PATH      "${CMAKE_CURRENT_SOURCE_DIR}/src/main/assets/${ANDROID_ABI}")

# Apps
set(BENTO4_APPS "Aac2Mp4;Mp42Aac;Mp42Ts;Mp42Hls;Mp4Compact;Mp4DcfPackager;Mp4Decrypt;Mp4Dump;Mp4Edit;Mp4Encrypt;Mp4Extract;Mp4Fragment;Mp4Info;Mp4Mux;Mp4Split;Mp4Tag")
foreach(app ${BENTO4_APPS})
  add_executable(${app} ${SOURCE_ROOT}/Apps/${app}/${app}.cpp)
  target_link_libraries(${app} ap4)
endforeach()
