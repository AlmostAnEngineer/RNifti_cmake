cmake_minimum_required(VERSION 3.29)
project(RNifti)

execute_process(
        COMMAND bash -c "cd ${CMAKE_SOURCE_DIR}/src/zlib"
        COMMAND bash -c "./configure --static && make"
)

file(COPY ${CMAKE_SOURCE_DIR}/src/zlib/libz.a DESTINATION ${CMAKE_BINARY_DIR})


install(FILES ${CMAKE_BINARY_DIR}/zlib/libz.a DESTINATION lib)

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
link_libraries(${CMAKE_BINARY_DIR})

add_library(nifty STATIC ${NIFTILIB_SRC} ${NIFTILIB_INC})
add_library(zlib STATIC ${ZLIB_INC} ${ZLIB_SRC})

add_executable(nii_info standalone/nii_info.cpp
        ${NIFTI_SRC}
        ${RNIFTI_INC}
        ${NIFTILIB_INC}
        ${ZLIB_INC}
)
target_link_libraries(nii_info nifty z zlib)

#install(TARGETS nifty DESTINATION lib)
#install(FILES ${CMAKE_BINARY_DIR}/libz.a DESTINATION lib)
#install(FILES ${NIFTILIB_INC} DESTINATION include)
#install(FILES ${RNIFTI_INC} DESTINATION include)
