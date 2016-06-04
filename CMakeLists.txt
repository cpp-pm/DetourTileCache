#
# Copyright (c) 2008-2016 the Urho3D project.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
#

# Note: This project depends on Detour

cmake_minimum_required(VERSION 3.0)

### Hunter snapshot that will be used ###
include("cmake/HunterGate.cmake")

#TODO: Need to update hunter, use local HUNTER_ROOT for initial testing
HunterGate(
    URL "https://github.com/ruslo/hunter/archive/v0.14.19.tar.gz" 
    SHA1 "ab886ddc6be6870a403224ee8f587e640827b1d9"
)

project(DetourTileCache VERSION 1.0.0)

hunter_add_package(Detour)
find_package(Detour CONFIG REQUIRED)

set(TARGET_SOURCE
  source/DetourTileCache.cpp
  source/DetourTileCacheBuilder.cpp
  )

set(TARGET_PUBLIC_HDRS 
  include/DetourTileCache.h
  include/DetourTileCacheBuilder.h
  )

include_directories(include)

# Add this explicity, since DetourTileCache doesn't add include folder
include_directories("${DETOUR_ROOT}/include/Detour") 

add_library(DetourTileCache ${TARGET_SOURCE})
target_link_libraries(DetourTileCache Detour::Detour)

# get_cmake_property(_variableNames VARIABLES)
# foreach (_variableName ${_variableNames})
#   message(STATUS "VARIABLE: ${_variableName}=${${_variableName}}")
# endforeach()

#### install 

set(config_install_dir "lib/cmake/${PROJECT_NAME}")
set(include_install_dir "include")

set(generated_dir "${CMAKE_CURRENT_BINARY_DIR}/generated")

# Configuration
set(version_config "${generated_dir}/${PROJECT_NAME}ConfigVersion.cmake")
set(project_config "${generated_dir}/${PROJECT_NAME}Config.cmake")
set(targets_export_name "${PROJECT_NAME}Targets")
set(namespace "${PROJECT_NAME}::")

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
  "${version_config}" COMPATIBILITY SameMajorVersion
  )

configure_package_config_file(
  "cmake/Config.cmake.in"
  "${project_config}"
  INSTALL_DESTINATION "${config_install_dir}"
  )

install(
  TARGETS DetourTileCache
  EXPORT "${targets_export_name}"
  LIBRARY DESTINATION "lib"
  ARCHIVE DESTINATION "lib"
  RUNTIME DESTINATION "bin"
  INCLUDES DESTINATION "${include_install_dir}"
  )

foreach(HEADER ${TARGET_PUBLIC_HDRS})
  install(
    FILES "${CMAKE_CURRENT_SOURCE_DIR}/${HEADER}"
    DESTINATION "${include_install_dir}/${PROJECT_NAME}"
    )
endforeach()

install(
  FILES "${project_config}" "${version_config}"
  DESTINATION "${config_install_dir}"
  )

install(
  EXPORT "${targets_export_name}"
  NAMESPACE "${namespace}"
  DESTINATION "${config_install_dir}"
  )
