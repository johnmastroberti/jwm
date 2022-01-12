cmake_minimum_required(VERSION 3.10)
project(jwm)

# Dependencies
find_package(X11 REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(YAJL REQUIRED yajl)


# Source files
include_directories(${PROJECT_SOURCE_DIR}/include)
file(GLOB sources ${PROJECT_SOURCE_DIR}/src/*.cpp)
file(GLOB headers ${PROJECT_SOURCE_DIR}/include/*.hpp)

# Compiler settings
## Warnings
SET(WARNING_FLAGS 
  "-Wall \
  -Wextra \
  -Wpedantic \
  -Wshadow \
  -Wconversion \
  -Wredundant-decls \
  -Wcast-align \
  -Wmissing-include-dirs \
  -Wswitch-enum \
  -Wswitch-default \
  -Winvalid-pch \
  -Wredundant-decls \
  -Wformat=2 \
  -Wmissing-format-attribute \
  -Wformat-nonliteral \
  -Wno-float-conversion"
)
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${WARNING_FLAGS}")

# Enable Address Sanitizer and UB Sanitizer for debug builds
SET(ASAN_FLAGS "-fsanitize=address -fno-optimize-sibling-calls -fsanitize-address-use-after-scope -fno-omit-frame-pointer")
SET(UBSAN_FLAGS "-fsanitize=undefined")

SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${ASAN_FLAGS} ${UBSAN_FLAGS}")
SET(CMAKE_LINKER_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} ${ASAN_FLAGS} ${UBSAN_FLAGS}")

## Set C++ standard to C++20
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Output compile commands for use with clangd lsp
SET(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Define the executable and libraries to be linked
add_executable(jwm ${sources} ${headers})
target_link_libraries(jwm X11::Xft X11::X11 X11::Xinerama ${YAJL_LIBRARIES})
target_include_directories(jwm PUBLIC ${YAJL_INCLUDE_DIRECTORIES})
target_compile_options(jwm PUBLIC ${YAJL_CFLAGS_OTHER})
# target_link_libraries(jwm fmt::fmt spdlog::spdlog range-v3::range-v3)
