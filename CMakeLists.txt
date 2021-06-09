cmake_minimum_required(VERSION 3.20.0)

project(BatchAcqusition)

file(GLOB 3DTS_SRC CONFIGURE_DEPENDS
    "*.h"
    "*.cpp"
)

if (MSVC)
    foreach(src_file ${3DTS_SRC})
        set_source_files_properties(
            ${src_file}
            PROPERTIES
            COMPILE_FLAGS "/YuBatchAcquisitionStdAfx.h"
        )
    endforeach(src_file ${3DTS_SRC})
    set_source_files_properties(
        BatchAcquisitionStdAfx.cpp
        PROPERTIES
        COMPILE_FLAGS "/YcBatchAcquisitionStdAfx.h"
    )
endif (MSVC)

add_executable(${PROJECT_NAME} ${3DTS_SRC})
add_definitions(-D_UNICODE)

OPTION(USE_PYLON "Implements Pylons SDK to build." OFF)
if(USE_PYLON)
    add_definitions(-DHAVE_PYLON_SDK)
    SET(PYLON_DIR "" CACHE PATH "Path to Pylons SDK directory")
    if(NOT PYLON_DIR STREQUAL "")
        set(PYLON_DIR_PATH "${PYLON_DIR}/Development/include")
        if(CMAKE_SIZEOF_VOID_P EQUAL 8)
            set(PYLON_LIB_PATH "${PYLON_DIR}/Development/lib/x64")
        elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
            set(PYLON_LIB_PATH "${PYLON_DIR}/Development/lib/Win32")
        endif()
        target_include_directories(${PROJECT_NAME} PUBLIC ${PYLON_DIR_PATH})
        target_link_directories(${PROJECT_NAME} PUBLIC ${PYLON_LIB_PATH})
    else()
        message(STATUS "Path to Pylons SDK directory not specified.")
    endif(NOT PYLON_DIR STREQUAL "")
endif(USE_PYLON)


OPTION(USE_SAPERA "Implements Saperas SDK to build." OFF)
if(USE_SAPERA)
    add_definitions(-DHAVE_SAPERA_SDK)
    SET(SAPERA_DIR "" CACHE PATH "Path to Saperas SDK directory")
    if(NOT SAPERA_DIR STREQUAL "")
        set(SAPERA_DIR_PATH "${SAPERA_DIR}/Include")
        set(SAPERA_DIR_PATH_CLASSES "${SAPERA_DIR}/Classes/Basic")
        if(CMAKE_SIZEOF_VOID_P EQUAL 8)
            set(SAPERA_LIB_PATH "${SAPERA_DIR}/Lib/Win64")
        elseif(CMAKE_SIZEOF_VOID_P EQUAL 4)
            set(SAPERA_LIB_PATH "${SAPERA_DIR}/Lib/Win32")
        endif()
        
        target_include_directories(${PROJECT_NAME} PUBLIC ${SAPERA_DIR_PATH})
        target_include_directories(${PROJECT_NAME} PUBLIC ${SAPERA_DIR_PATH_CLASSES})

        file(GLOB SAPERA ${SAPERA_LIB_PATH}/*.lib)
        target_link_libraries(${PROJECT_NAME} ${SAPERA})
    else()
        message(STATUS "Path to Saperas SDK directory not specified.")
    endif(NOT SAPERA_DIR STREQUAL "")
endif(USE_SAPERA)


OPTION(USE_FLYCAPTURE2 "Implements FlyCapture2s SDK to build." OFF)
if(USE_FLYCAPTURE2)
    add_definitions(-DHAVE_FLYCAPTURE2_SDK)
    SET(FLYCAPTURE2_DIR "" CACHE PATH "Path to FlyCapture2s SDK directory")
    if(NOT FLYCAPTURE2_DIR STREQUAL "")
        set(FLYCAPTURE2_DIR_PATH "${FLYCAPTURE2_DIR}/include")
        target_include_directories(${PROJECT_NAME} PUBLIC ${FLYCAPTURE2_DIR_PATH})

        if(MSVC_VERSION STREQUAL "1800")
            set(FLYCAPTURE2_LIB_PATH "${FLYCAPTURE2_DIR}/lib64/vs2013")
        elseif(MSVC_VERSION STREQUAL "1900")
            set(FLYCAPTURE2_LIB_PATH "${FLYCAPTURE2_DIR}/lib64/vs2015")
        else()
            set(FLYCAPTURE2_LIB_PATH "${FLYCAPTURE2_DIR}/lib64")
        endif(MSVC_VERSION STREQUAL "1800")

        file(GLOB LIBS ${FLYCAPTURE2_LIB_PATH}/*.lib)
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
        message(STATUS "Path to FlyCapture2s directory not specified.")
    endif(NOT FLYCAPTURE2_DIR STREQUAL "")
endif(USE_FLYCAPTURE2)

find_package(VTK REQUIRED)
target_include_directories(${PROJECT_NAME} PUBLIC ${VTK_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} ${VTK_LIBRARIES})

find_package(OpenCV REQUIRED)
target_include_directories(${PROJECT_NAME} PUBLIC ${OpenCV_INCLUDE_DIRS})
target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
