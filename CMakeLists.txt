cmake_minimum_required(VERSION 3.0)
project(bprinter VERSION 1.0.0)

# Boost Karma (part of Boost Spirir) has ability to handle output
option(USE_BOOST_KARMA "Use Boost Karma for output instead of doing it ourselves?" OFF)
if(USE_BOOST_KARMA)
  find_package(Boost)
  if (Boost_FOUND)
    add_definitions("-DUSE_BOOST_KARMA")
  endif (Boost_FOUND)
endif(USE_BOOST_KARMA)

include_directories(${PROJECT_SOURCE_DIR}/include)

set(LIBRARY_OUTPUT_PATH ${CMAKE_CURRENT_SOURCE_DIR}/lib)
add_library(bprinter STATIC
  include/table_printer.h
  include/impl/table_printer.tpp.h
  src/table_printer.cpp
)

add_executable(bprinterTest src/test_main.cxx)
target_link_libraries(bprinterTest bprinter)

set(BP_TARGET_NAME ${PROJECT_NAME})
set(BP_PACKAGE_NAME ${BP_TARGET_NAME})
set(BP_CONFIG_IN_FILENAME "config.cmake.in")
set(BP_CONFIG_FILENAME "${BP_PACKAGE_NAME}Config.cmake")
set(BP_CONFIGVERSION_FILENAME "${BP_PACKAGE_NAME}ConfigVersion.cmake")
set(BP_CONFIG_DESTINATION "cmake")
set(BP_INCLUDE_INSTALL_DIR "include")
set(BP_LIB_INSTALL_DIR lib)

include(CMakePackageConfigHelpers)
configure_package_config_file(${BP_CONFIG_IN_FILENAME} ${CMAKE_CURRENT_BINARY_DIR}/${BP_CONFIG_FILENAME}
	INSTALL_DESTINATION ${BP_CONFIG_DESTINATION}
	PATH_VARS BP_INCLUDE_INSTALL_DIR BP_LIB_INSTALL_DIR)
write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/${BP_CONFIGVERSION_FILENAME} 
	VERSION ${PROJECT_VERSION}
	COMPATIBILITY SameMajorVersion)

INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/${BP_CONFIG_FILENAME} ${CMAKE_CURRENT_BINARY_DIR}/${BP_CONFIGVERSION_FILENAME}
	DESTINATION ${BP_CONFIG_DESTINATION})

INSTALL(TARGETS bprinter 
	DESTINATION ${BP_LIB_INSTALL_DIR} 
	PERMISSIONS OWNER_READ GROUP_READ WORLD_READ)

INSTALL(FILES include/table_printer.h 
	DESTINATION ${BP_INCLUDE_INSTALL_DIR} 
	PERMISSIONS OWNER_READ GROUP_READ WORLD_READ)

INSTALL(FILES include/impl/table_printer.tpp.h 
	DESTINATION ${BP_INCLUDE_INSTALL_DIR}/impl 
	PERMISSIONS OWNER_READ GROUP_READ WORLD_READ)

