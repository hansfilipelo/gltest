cmake_minimum_required(VERSION 3.0) # setting this is required
project(gltest)            # this sets the project name

file(GLOB_RECURSE sources      src/*.c src/*.h)
file(GLOB_RECURSE resources resources/*)

add_executable(gltest ${sources} ${data})

target_compile_options(gltest PUBLIC -Wall -Werror)

## dependencies
# macOS needs special treatment
if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  set(MACOSEXTRALINKLIB "-framework Cocoa -framework OpenGL -framework IOKit -framework CoreVideo -framework Metal -framework QuartzCore")
else()
	set(MACOSEXTRALINKLIB "")
endif()

find_package(PkgConfig REQUIRED)
pkg_search_module(GLFW REQUIRED glfw3)
find_package(OpenGL REQUIRED)
find_package(glfw3 REQUIRED)

target_include_directories(gltest PRIVATE ${GLFW_INCLUDE_DIRS} ${OPENGL_INCLUDE_DIR})
target_link_directories(gltest PRIVATE ${GLFW_LIBDIR})
target_link_libraries(gltest
  ${GLFW_LIBRARIES}
  ${OPENGL_gl_LIBRARY}
  ${MACOSEXTRALINKLIB}
)

## install
if(NOT CMAKE_INSTALL_DIR)
  set(CMAKE_INSTALL_DIR /usr/local)
endif()
install(TARGETS gltest DESTINATION ${CMAKE_INSTALL_DIR}/bin)
install(DIRECTORY resources DESTINATION ${CMAKE_INSTALL_DIR}/share/gltest)

# CPack package
set(CPACK_PACKAGE_NAME "GLtest")
set(CPACK_PACKAGE_VERSION "0.1.0")

# We don't want to split our program up into several incomplete pieces.
set(CPACK_MONOLITHIC_INSTALL 1)

# This must be last
include(CPack)
