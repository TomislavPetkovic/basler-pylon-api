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


SET(BASLER_DIR "Default" CACHE STRING "Path to basler directory")
if(NOT BASLER_DIR STREQUAL "Default")
    set(BASLER_DIR_PATH "${BASLER_DIR}/pylon 6/Development/include")
    set(BASLER_LIB_PATH "${BASLER_DIR}/pylon 6/Development/lib/x64")

    target_include_directories(${PROJECT_NAME} PUBLIC ${BASLER_DIR_PATH})
    target_link_directories(${PROJECT_NAME} PUBLIC ${BASLER_LIB_PATH})
else()
    message(STATUS "Please specify path to basler directory")
endif(NOT BASLER_DIR STREQUAL "Default")


SET(SAPERA_DIR "Default" CACHE STRING "Path to sapera directory")
if(NOT SAPERA_DIR STREQUAL "Default")
    set(SAPERA_DIR_PATH "${SAPERA_DIR}/Sapera/Include")
    set(SAPERA_DIR_PATH_CLASSES "${SAPERA_DIR}/Sapera/Classes/Basic")
    set(SAPERA_LIB_PATH "${SAPERA_DIR}/Sapera/Lib/Win64")

    target_include_directories(${PROJECT_NAME} PUBLIC ${SAPERA_DIR_PATH})
    target_include_directories(${PROJECT_NAME} PUBLIC ${SAPERA_DIR_PATH_CLASSES})
    file(GLOB SAPERA ${SAPERA_LIB_PATH}/*.lib)
    target_link_libraries(${PROJECT_NAME} ${SAPERA})
else()
    message(STATUS "Please specify path to sapera directory")
endif(NOT SAPERA_DIR STREQUAL "Default")


SET(FLYCAPTURE_DIR "Default" CACHE STRING "Path to flycapture directory")
if(NOT FLYCAPTURE_DIR STREQUAL "Default")
    set(FLYCAPTURE_DIR_PATH "${FLYCAPTURE_DIR}/FlyCapture2/include")
    set(FLYCAPTURE_LIB_PATH "${FLYCAPTURE_DIR}/FlyCapture2/lib64")

    target_include_directories(${PROJECT_NAME} PUBLIC ${FLYCAPTURE_DIR_PATH})

    file(GLOB RES ${FLYCAPTURE_LIB_PATH}/*.lib)
    if(CMAKE_BUILD_TYPE STREQUAL "Debug")
        list(REMOVE_AT RES 0 2 4 6 8)
    else()
        list(REMOVE_AT RES 1 3 5 7 9)
    endif()
    target_link_libraries(${PROJECT_NAME} ${RES})
else()
    message(STATUS "Please specify path to flycapture directory")
endif(NOT FLYCAPTURE_DIR STREQUAL "Default")


SET(VTK_DIR "Default" CACHE STRING "Path to vtk directory")
if(NOT VTK_DIR STREQUAL "Default")
    find_package(VTK REQUIRED)
    target_include_directories(${PROJECT_NAME} PUBLIC ${VTK_INCLUDE_DIRS})
    target_link_libraries(${PROJECT_NAME} ${VTK_LIBRARIES})
else()
    message(STATUS "Please specify path to vtk directory")
endif(NOT VTK_DIR STREQUAL "Default")


SET(OpenCV_DIR "Default" CACHE STRING "Path to opencv directory")
if(NOT OpenCV_DIR STREQUAL "Default")
    find_package(OpenCV REQUIRED)
    target_include_directories(${PROJECT_NAME} PUBLIC ${OpenCV_INCLUDE_DIRS})
    target_link_libraries(${PROJECT_NAME} ${OpenCV_LIBS})
else()
    message(STATUS "Please specify path to opencv directory")
endif(NOT OpenCV_DIR STREQUAL "Default")

add_definitions(-D_UNICODE)