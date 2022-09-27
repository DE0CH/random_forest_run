cmake_minimum_required (VERSION 2.8 FATAL_ERROR)
project (RFR CXX)

# add a version number
set (RFR_VERSION_MAJOR 0)
set (RFR_VERSION_MINOR 8)
set (RFR_VERSION_RELEASE 3)

# Print debug information about boost
set(Boost_DEBUG 1)

IF(NOT CMAKE_BUILD_TYPE)
 SET(CMAKE_BUILD_TYPE "DEBUG")
 SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} --coverage") # enabling coverage
 #SET(CMAKE_EXE_LINKER_FLAGS "-fprofile-arcs -ftest-coverage")

 #SET(CMAKE_BUILD_TYPE "RELESE")
 #SET(CMAKE_BUILD_TYPE "RELWITHDEBINFO")
 #SET(CMAKE_BUILD_TYPE "MINSIZEREL")
ENDIF()

# required libaries
find_package(Boost COMPONENTS unit_test_framework)
find_package(Doxygen)
find_package(PythonInterp 3)
find_package(SWIG)

enable_testing()

# define all include directories within the project for different splits, data containers, node types etc...
include_directories ("${PROJECT_SOURCE_DIR}/include")
include_directories ("${Boost_INCLUDE_DIR}")


# the documentation
if(DOXYGEN_FOUND)
	configure_file(${PROJECT_SOURCE_DIR}/Doxyfile ${CMAKE_CURRENT_BINARY_DIR}/Doxyfile @ONLY)
	add_custom_target(doxygen_doc
		COMMAND	${DOXYGEN_EXECUTABLE} Doxyfile
		WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}
		COMMENT "Generating API documentation with Doxygen" VERBATIM)
endif(DOXYGEN_FOUND)

# add all the unit tests in the tests directory
add_subdirectory(tests)


IF(CMAKE_COMPILER_IS_GNUCXX)
  ADD_DEFINITIONS("-Wall -O0 -g")
ELSEIF(${CMAKE_CXX_COMPILER_ID} MATCHES "Clang")
  ADD_DEFINITIONS("-Wall -fPIC -pg -std=c++11")
ELSE()
  MESSAGE(FATAL_ERROR "CMakeLists.txt has not been tested/written for your compiler.")
ENDIF()
