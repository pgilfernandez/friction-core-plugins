# SPDX-License-Identifier: GPL-3.0-only

cmake_minimum_required(VERSION 3.12)
project(SvgImportPlugin)

find_package(Qt5 COMPONENTS Core Xml Qml Gui Widgets REQUIRED)

set(CMAKE_AUTOMOC ON)
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_library(${PROJECT_NAME} MODULE
    svgimportplugin.cpp
    svganimationimportdialog.cpp
    svganimationimportdialog.h
    svganimationimporter.cpp
    svganimationimporter.h
    plugin.json
)

target_include_directories(${PROJECT_NAME} PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/../../core
    ${CMAKE_CURRENT_SOURCE_DIR}/../../ui
    ${CMAKE_CURRENT_SOURCE_DIR}/../../skia
    $<TARGET_PROPERTY:frictioncore,INCLUDE_DIRECTORIES>
)

target_link_directories(${PROJECT_NAME} PRIVATE
    $<TARGET_PROPERTY:frictioncore,LINK_DIRECTORIES>
)

target_link_libraries(${PROJECT_NAME} PRIVATE
    Qt5::Core
    Qt5::Xml
    Qt5::Qml
    Qt5::Gui
    Qt5::Widgets
    frictioncore
)

if(WIN32)
    target_link_libraries(${PROJECT_NAME} PRIVATE skia user32 opengl32)
else()
    target_link_libraries(${PROJECT_NAME} PRIVATE skia-friction)
endif()

set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "")
if(APPLE)
    set_target_properties(${PROJECT_NAME} PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY
            ${CMAKE_BINARY_DIR}/src/app/friction.app/Contents/MacOS/plugins
        RUNTIME_OUTPUT_DIRECTORY
            ${CMAKE_BINARY_DIR}/src/app/friction.app/Contents/MacOS/plugins
        BUILD_RPATH "${CMAKE_BINARY_DIR}/src/core"
    )
else()
    set_target_properties(${PROJECT_NAME} PROPERTIES
        LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/src/app/plugins
        RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/src/app/plugins
    )
endif()

add_dependencies(all_friction_plugins ${PROJECT_NAME})
