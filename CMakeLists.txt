cmake_minimum_required(VERSION 3.20.0)
project(imgui-sdl-template)

# Specify the C++ standard.
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Export compile_commands.json file.
set(CMAKE_EXPORT_COMPILE_COMMANDS True)

execute_process(COMMAND uname OUTPUT_VARIABLE uname)
if (uname MATCHES "^MSYS" OR uname MATCHES "^MINGW")
    set(MSYS2 TRUE)
    message(STATUS "This is MSYS")
else()
    set(MSYS2 FALSE)
    message(STATUS "This is not MSYS")
endif()

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

# Setup source locations.
set(SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(SOURCES
    ${SOURCE_DIR}/main.cpp
    ${SOURCE_DIR}/bindings/imgui_impl_sdl.cpp
    ${SOURCE_DIR}/bindings/imgui_impl_opengl3.cpp
)
set(HEADERS
    ${SOURCE_DIR}/bindings/imgui_impl_sdl.h
    ${SOURCE_DIR}/bindings/imgui_impl_opengl3.h
)

# Find external packages.
find_package(OpenGL REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(SDL2 REQUIRED sdl2)

# Executable and set WIN32 to make sure it's GUI application, not console application.
# Console application in this case means that on Windows, starting the program will open
# terminal window beside the GUI application.
set(EXECUTABLE imgui_example)
add_executable(${EXECUTABLE} WIN32)
target_sources(${EXECUTABLE} PUBLIC ${HEADERS} PRIVATE ${SOURCES})

# Enable compiler warnings.
target_compile_options(${EXECUTABLE} PRIVATE -Wall -Wformat -Wpedantic -Werror)

target_include_directories(
    ${EXECUTABLE}
    PRIVATE ${SOURCE_DIR}/bindings
)

# On Windows MSYS2 link libraries statically to the binary. This way binary can
# be more easily distributed and does not depend on MSY2 being installed. On
# Linux just load used shared libraries instead.
if (MSYS2)
    target_link_libraries(${EXECUTABLE} ${SDL2_STATIC_LIBRARIES} ${CONAN_LIBS} OpenGL::GL)
    target_link_libraries(${EXECUTABLE} -static)
    target_include_directories(
        ${EXECUTABLE}
        PUBLIC ${SDL2_STATIC_INCLUDE_DIRS}
    )
else()
    target_link_libraries(${EXECUTABLE} ${SDL2_LIBRARIES} OpenGL::GL)
    target_link_libraries(${EXECUTABLE} ${CONAN_LIBS})
    target_include_directories(
        ${EXECUTABLE}
        PUBLIC ${SDL2_INCLUDE_DIRS}
    )
endif()

target_compile_options(${EXECUTABLE} PUBLIC ${SDL2_CFLAGS_OTHER})
