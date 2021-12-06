add_library(MathFunctions mysqrt.cxx)

# state that anybody linking to us needs to include the current source dir
# to find MathFunctions.h, while we don't.
target_include_directories(MathFunctions
          INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}
          )

# does this system provide the log and exp functions ?
# almost every platform has log and exp functions
include(CheckSymbolExists)
# Check the log function is available after including math.h
# and store the result in HAVE_LOG
check_symbol_exists(log "math.h" HAVE_LOG)
check_symbol_exists(exp "math.h" HAVE_EXP)
# on some platforms, need to link to the m library
# if log and exp are initially found, require the m library and try again
if (NOT (HAVE_LOG AND HAVE_EXP))
    unset(HAVE_LOG CACHE)
    unset(HAVE_EXP CACHE)
    set(CMAKE_REQUIRE_LIBRARIES "m")
    check_symbol_exists(log "math.h" HAVE_LOG)
    check_symbol_exists(exp "math.h" HAVE_EXP)
    if (HAVE_LOG AND HAVE_EXP)
        target_link_libraries(MathFunctions PRIVATE m)
    endif()
endif()

# if available, specify HAVE_LOG and HAVE_EXP as PRIVATE compile definitions
if (HAVE_LOG AND HAVE_EXP)
    target_compile_definitions(MathFunctions
            PRIVATE "HAVE_LOG" "HAVE_EXP")   
endif()

# install rules
install(TARGETS MathFunctions DESTINATION lib)
install(FILES MathFunctions.h DESTINATION include)
