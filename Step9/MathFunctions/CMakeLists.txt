# first we add the executable that generates the table
# add_executable(MakeTable MakeTable.cxx)

# add the command to generate the source code
# add_custom_command(
#   OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
#   COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
#   DEPENDS MakeTable
#   )

# add the main library
# add_library(MathFunctions
#             mysqrt.cxx
#             ${CMAKE_CURRENT_BINARY_DIR}/Table.h
#             )
# add the library that runs
add_library(MathFunctions MathFunctions.cxx)

# state that anybody linking to us needs to include the current source dir
# to find MathFunctions.h, while we don't.
# state that we depend on our binary dir to find Table.h
# target_include_directories(MathFunctions
#           INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
#           PRIVATE   ${CMAKE_CURRENT_BINARY_DIR}
#           )

# state that anybody linking to us needs to include the current source dir
# to find MathFunctions.h, while we don't
target_include_directories(MathFunctions
                          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
                          ) 

# let MathFunctions to become a real library that encapsulates using mysqrt
# or sqrt, instead of requiring the calling code to do this logic.
# This will also mean that USE_MYMATH will not control building MathFunctions,
# but instead will control the behavior of this library.  
option(USE_MYMATH "Use tutorial provided math implementation" ON)

if (USE_MYMATH)
  target_compile_definitions(MathFunctions PRIVATE "USE_MYMATH")

  # first we add the executable that generates the table
  add_executable(MakeTable MakeTable.cxx)

  # add the command to generate the source code 
  add_custom_command(
    OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
    COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
    DEPENDS MakeTable
  )

  # library that just does sqrt
  add_library(SqrtLibrary STATIC
              mysqrt.cxx 
              ${CMAKE_CURRENT_BINARY_DIR}/Table.h
              )   
  
  # state that we depend on our binary dir to find Table.h
  target_include_directories(SqrtLibrary PRIVATE 
                            ${CMAKE_CURRENT_BINARY_DIR}
                            )

  # state that SqrtLibrary need PIC when the default is shared libraries
  set_target_properties(SqrtLibrary PROPERTIES
                        POSITION_INDEPENDENT_CODE ${BUILD_SHARED_LIBS}
                        )

  target_link_libraries(MathFunctions PRIVATE SqrtLibrary)
endif()

# define the symbol stating we are using the declspec(dllexport) when building on windows
target_compile_definitions(MathFunctions PRIVATE "EXPLORING_MYMATH")

# install rules
set(installable_libs MathFunctions)
if (TARGET SqrtLibrary)
  list(APPEND installable_libs SqrtLibrary)
endif()

install(TARGETS ${installable_libs} DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)
