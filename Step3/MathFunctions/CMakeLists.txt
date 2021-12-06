add_library(MathFunctions mysqrt.cxx)

target_include_directories(MathFunctions
    # use the INTERFACE usage requirement
    # because anybody linking to MathFunctions needs to include the current source directory, 
    # while MathFunctions itself doesn't.
    INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}    
)