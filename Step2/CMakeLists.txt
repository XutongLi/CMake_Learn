cmake_minimum_required(VERSION 3.10)

# set the project name and version
project(Tutorial VERSION 1.0)

# specify the C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# make the MathFunctions library optional
# this setting will be stored in the cache so that the user does not
# need to set the value each time they run CMake on a build directory
option(USE_MYMATH "Use tutorial provided math implementation" ON)

# configure a header file to pass some of the CMake settings
# to the source code
configure_file(TutorialConfig.h.in TutorialConfig.h)

# add the MathFunctions library
# add_subdirectory(MathFunctions)

if (USE_MYMATH)
    # add the MathFunctions library
    add_subdirectory(MathFunctions)
    # add the library MathFuncitons to EXTRA_LIBS list
    list(APPEND EXTRA_LIBS MathFunctions)
    # add the include directory to EXTRA_INCLIDES list 
    list(APPEND EXTRA_INCLUDES "${PROJECT_SOURCE_DIR}/MathFunctions")
endif()

# add the executable
add_executable(Tutorial tutorial.cxx)

# link the library to the executable
# target_link_libraries(Tutorial PUBLIC MathFunctions)
target_link_libraries(Tutorial PUBLIC ${EXTRA_LIBS})

# add the binary tree to the search path for include files
# so that we will find TutorialConfig.h
# add MathFunctions as an include directory so that the mysqrt.h header
# file can be found
target_include_directories(Tutorial PUBLIC
                           "${PROJECT_BINARY_DIR}"
                           #"${PROJECT_SOURCE_DIR}/MathFunctions"
                           ${EXTRA_INCLUDES}
                           )
