cmake_minimum_required(VERSION 2.8)

if(POLICY CMP0048)
  cmake_policy(SET CMP0048 NEW)
endif()

project(xsde-cmake VERSION 1.0.0)

# include directories to make them reachable by #include commands 
include_directories(
	# include library directory 
	# so #include <xsde/something.h> works everywhere in the project
	${CMAKE_CURRENT_SOURCE_DIR}/libxsde

	# include directory with generated config headers
	# so #include <xsde/config.h> also works
	${CMAKE_CURRENT_BINARY_DIR}/libxsde/xsde/include
)

# adding subdirectories to build nested CMakeLists.txt files
add_subdirectory(libxsde) # library
add_subdirectory(example-hybrid-hello) # example

include(CMakePackageConfigHelpers)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/XsdeConfigVersion.cmake"
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY AnyNewerVersion
)

configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/XsdeConfig.cmake.in"
    "${CMAKE_CURRENT_BINARY_DIR}/xsde-config.cmake"
    INSTALL_DESTINATION lib/cmake/xsde
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/xsde-config.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/XsdeConfigVersion.cmake"
    DESTINATION lib/cmake/xsde
)

install(EXPORT xsdeTargets
    FILE xsdeTargets.cmake
    NAMESPACE xsde::
    DESTINATION lib/cmake/xsde
)

install(DIRECTORY libxsde/xsde DESTINATION include PATTERN "*.txt" EXCLUDE
                                            PATTERN "*.in" EXCLUDE
                                            PATTERN "makefile" EXCLUDE
                                            PATTERN "nmakefile" EXCLUDE)