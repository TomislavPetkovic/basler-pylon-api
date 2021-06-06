cmake_minimum_required(VERSION 3.20.0)

project(3dts)

file(GLOB 3DTS_SRC CONFIGURE_DEPENDS
    "*.h"
    "*.cpp"
)

if (MSVC)
    foreach( src_file ${3DTS_SRC} )
        set_source_files_properties(
            ${src_file}
            PROPERTIES
            COMPILE_FLAGS "/YuBatchAcquisitionStdAfx.h"
            )
    endforeach( src_file ${3DTS_SRC} )
    set_source_files_properties(BatchAcquisitionStdAfx.cpp
        PROPERTIES
        COMPILE_FLAGS "/YcBatchAcquisitionStdAfx.h"
        )
endif (MSVC)

add_executable(${PROJECT_NAME} ${3DTS_SRC})

add_definitions(-D_UNICODE)


SET(BASLER_DIR "Default" CACHE PATH "Path to basler directory")
if(NOT BASLER_DIR STREQUAL "Default")
    set(BASLER_DIR_PATH "${BASLER_DIR}/pylon 6/Development/include")
    if(CMAKE_SIZEOF_VOID_P EQUAL 8)
        set(BASLER_LIB_PATH "${BASLER_DIR}/pylon 6/Development/lib/x64")
    elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
        set(BASLER_LIB_PATH "${BASLER_DIR}/pylon 6/Development/lib/Win32")
    endif()
    include_directories(${PROJECT_NAME} PUBLIC ${BASLER_DIR_PATH})
    target_link_directories(${PROJECT_NAME} PUBLIC ${BASLER_LIB_PATH})
else()
    message(STATUS "Path to basler directory not specified.")
endif(NOT BASLER_DIR STREQUAL "Default")


SET(SAPERA_DIR "Default" CACHE PATH "Path to sapera directory")
if(NOT SAPERA_DIR STREQUAL "Default")
    set(SAPERA_DIR_PATH "${SAPERA_DIR}/Sapera/Include")
    set(SAPERA_DIR_PATH_CLASSES "${SAPERA_DIR}/Sapera/Classes/Basic")
    if(CMAKE_SIZEOF_VOID_P EQUAL 8)
        set(SAPERA_LIB_PATH "${SAPERA_DIR}/Sapera/Lib/Win64")
    elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
        set(SAPERA_LIB_PATH "${SAPERA_DIR}/Sapera/Lib/Win32")
    endif()
    
    target_include_directories(${PROJECT_NAME} PUBLIC ${SAPERA_DIR_PATH})
    target_include_directories(${PROJECT_NAME} PUBLIC ${SAPERA_DIR_PATH_CLASSES})

    file(GLOB SAPERA ${SAPERA_LIB_PATH}/*.lib)
    target_link_libraries(${PROJECT_NAME} ${SAPERA})
else()
    message(STATUS "Path to sapera directory not specified.")
endif(NOT SAPERA_DIR STREQUAL "Default")


SET(FLYCAPTURE_DIR "Default" CACHE PATH "Path to flycapture directory")
if(NOT FLYCAPTURE_DIR STREQUAL "Default")
    set(FLYCAPTURE_DIR_PATH "${FLYCAPTURE_DIR}/FlyCapture2/include")
    target_include_directories(${PROJECT_NAME} PUBLIC ${FLYCAPTURE_DIR_PATH})

    if(MSVC_VERSION STREQUAL "1800")
        set(FLYCAPTURE_LIB_PATH "${FLYCAPTURE_DIR}/FlyCapture2/lib64/vs2013")
    elseif(MSVC_VERSION STREQUAL "1900")
        set(FLYCAPTURE_LIB_PATH "${FLYCAPTURE_DIR}/FlyCapture2/lib64/vs2015")
    else()
        set(FLYCAPTURE_LIB_PATH "${FLYCAPTURE_DIR}/FlyCapture2/lib64")
    endif(MSVC_VERSION STREQUAL "1800")

    file(GLOB LIBS ${FLYCAPTURE_LIB_PATH}/*.lib)
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        if(MSVC_VERSION STREQUAL "1800" OR MSVC_VERSION STREQUAL "1900")
            list(REMOVE_AT LIBS 0 2 4 6 8 10 12 14)
        else()
            list(REMOVE_AT LIBS 0 2 4 6 8)
        endif(MSVC_VERSION STREQUAL "1800" OR MSVC_VERSION STREQUAL "1900")
    else()
        if(MSVC_VERSION STREQUAL "1800" OR MSVC_VERSION STREQUAL "1900")
            list(REMOVE_AT LIBS 1 3 5 7 9 11 13 15)
        else()
            list(REMOVE_AT LIBS 1 3 5 7 9)
        endif(MSVC_VERSION STREQUAL "1800" OR MSVC_VERSION STREQUAL "1900")
    endif(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_link_libraries(${PROJECT_NAME} ${LIBS})
else()
    message(STATUS "Path to flycapture directory not specified.")
endif(NOT FLYCAPTURE_DIR STREQUAL "Default")

find_package(VTK REQUIRED)
target_include_directories(${PROJECT_NAME} PUBLIC ${VTK_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} ${VTK_LIBRARIES})

