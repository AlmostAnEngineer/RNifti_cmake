cmake_minimum_required(VERSION 3.29)
project(RNifti)

#add_custom_command(
#        OUTPUT libz.a
#        COMMAND "./configure --static && make"
#        WORKING_DIRECTORY ${CMAKE_SOURCE_DIR}/src/zlib
#)

execute_process(
    COMMAND "cd src/zlib"
    COMMAND "./configure --static && make"
)

file(COPY ${CMAKE_SOURCE_DIR}/src/zlib/libz.a DESTINATION ${CMAKE_BINARY_DIR})
file(GLOB_RECURSE NIFTILIB_INC "${CMAKE_BINARY_DIR}/include/niftilib/*.h")
file(GLOB RNIFTI_INC
        "${CMAKE_BINARY_DIR}/include/RNifty.h"
        "${CMAKE_BINARY_DIR}/include/RNiftyAPI.h"
        "${CMAKE_BINARY_DIR}/include/RNifti/*.h")
file(GLOB_RECURSE RNIFTY_INC "${CMAKE_BINARY_DIR}/include/RNifti/*.h")
file(GLOB ZLIB_INC "include/znzlib/znzlib.h")
file(GLOB ZLIB_SRC "src/znzlib/znzlib.c")

set(NIFTILIB_SRC
        "${CMAKE_SOURCE_DIR}/src/niftilib/nifti1_io.c"
        "${CMAKE_SOURCE_DIR}/src/niftilib/nifti2_io.c")

include_directories(inst/include/)
include_directories(src/zlib)

add_library(nifty STATIC ${NIFTILIB_SRC} ${NIFTILIB_INC})
add_library(znzlib STATIC ${ZLIB_INC} ${ZLIB_SRC})

install(TARGETS nifty DESTINATION lib)
install(TARGETS znzlib DESTINATION lib)
install(FILES build/libz.a DESTINATION lib)

install(
        DIRECTORY "inst/include/niftilib/"
        DESTINATION include
        FILES_MATCHING
        PATTERN "*.h"
)
install(
        DIRECTORY "inst/include/RNifti/"
        DESTINATION include
        FILES_MATCHING
        PATTERN "*.h"
)
install(FILES inst/include/RNifti.h DESTINATION include)
install(FILES inst/include/RNiftiAPI.h DESTINATION include)
