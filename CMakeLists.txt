cmake_minimum_required(VERSION 3.10)

# Add 16 KB page size support
set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -Wl,-z,max-page-size=16384")

add_library( k2pdfoptjni

             SHARED

             src/main/cpp/jni.cpp )

include_directories(src/main/cpp/)

add_definitions(-DUSE_CMAKE -DK2PDFOPT_KINDLEPDFVIEWER)

# Find pre-built libraries from libk2pdfopt subproject
file(GLOB K2PDFOPT_TARGET "${CMAKE_SOURCE_DIR}/libk2pdfopt/build/intermediates/cxx/*/obj/${ANDROID_ABI}/libk2pdfopt.so")
file(GLOB WILLUS_TARGET "${CMAKE_SOURCE_DIR}/libk2pdfopt/build/intermediates/cxx/*/obj/${ANDROID_ABI}/libwillus.so")

# Handle nested hash directories
if(NOT K2PDFOPT_TARGET)
    file(GLOB K2PDFOPT_TARGET "${CMAKE_SOURCE_DIR}/libk2pdfopt/build/intermediates/cxx/*/*/obj/${ANDROID_ABI}/libk2pdfopt.so")
endif()
if(NOT WILLUS_TARGET)
    file(GLOB WILLUS_TARGET "${CMAKE_SOURCE_DIR}/libk2pdfopt/build/intermediates/cxx/*/*/obj/${ANDROID_ABI}/libwillus.so")
endif()

add_library(k2pdfopt SHARED IMPORTED)
include_directories( libk2pdfopt/src/main/cpp/include_mod libk2pdfopt/src/main/cpp/k2pdfopt )
set_target_properties(k2pdfopt PROPERTIES IMPORTED_LOCATION ${K2PDFOPT_TARGET})

add_library(willus SHARED IMPORTED)
include_directories( libk2pdfopt/src/main/cpp/include_mod libk2pdfopt/src/main/cpp/willus )
set_target_properties(willus PROPERTIES IMPORTED_LOCATION ${WILLUS_TARGET})

find_library( log-lib log )
find_library( android-lib android )
find_library( jnigraphics-lib jnigraphics )

target_link_libraries( k2pdfoptjni
                       ${log-lib} ${android-lib} ${jnigraphics-lib} k2pdfopt willus )
