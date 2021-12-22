# add the executable for MakeTable
add_executable(MakeTable MakeTable.cxx)

# add a custom command that specifies how to produce Table.h by running MakeTable
add_custom_command(
  OUTPUT ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  COMMAND MakeTable ${CMAKE_CURRENT_BINARY_DIR}/Table.h
  DEPENDS MakeTable
)

add_library(MathFunctions 
            mysqrt.cxx
            # let CMake know that mysqrt.cxx depends on the generated file Table.h
            ${CMAKE_CURRENT_BINARY_DIR}/Table.h
            )

# state that anybody linking to us needs to include the current source dir
# to find MathFunctions.h, while we don't.
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          # add the current binary directory to the list of include directories,
          # so that Table.h can be found and included by mysqrt.cxx
          PRIVATE ${CMAKE_CURRENT_BINARY_DIR}
          )

# does this system provide the log and exp functions?
# include(CheckSymbolExists)
# check_symbol_exists(log "math.h" HAVE_LOG)
# check_symbol_exists(exp "math.h" HAVE_EXP)
# if(NOT (HAVE_LOG AND HAVE_EXP))
#   unset(HAVE_LOG CACHE)
#   unset(HAVE_EXP CACHE)
#   set(CMAKE_REQUIRED_LIBRARIES "m")
#   check_symbol_exists(log "math.h" HAVE_LOG)
#   check_symbol_exists(exp "math.h" HAVE_EXP)
#   if(HAVE_LOG AND HAVE_EXP)
#     target_link_libraries(MathFunctions PRIVATE m)
#   endif()
# endif()

# # add compile definitions
# if(HAVE_LOG AND HAVE_EXP)
#   target_compile_definitions(MathFunctions
#                              PRIVATE "HAVE_LOG" "HAVE_EXP")
# endif()

# install rules
install(TARGETS MathFunctions DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)
