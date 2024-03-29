cmake_minimum_required(VERSION 2.8)
set(CMAKE_OSX_DEPLOYMENT_TARGET "10.10" CACHE STRING "Minimum OS X deployment version")
project(Jammer)

set(CMAKE_CXX_STANDARD 17)

set(SOURCES main.cpp)

# libsoundio options
set(BUILD_EXAMPLE_PROGRAMS ON)
set(BUILD_TESTS OFF CACHE BOOL "Build tests")

add_subdirectory(libraries/libsoundio)

list(APPEND ASSETS
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFRegular.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFRegularItalic.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFBold.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFBoldItalic.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFMedium.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFMediumItalic.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/SFThin.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/PTMono.ttf
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/rhodes.ogg
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/klopfgeist_bar.ogg
    ${CMAKE_CURRENT_SOURCE_DIR}/assets/klopfgeist_beat.ogg
)

if(APPLE)
    set(ICON ${CMAKE_CURRENT_SOURCE_DIR}/assets/icon.icns)
    set(MACOSX_BUNDLE_ICON_FILE icon.icns)
    set_source_files_properties(${ICON} PROPERTIES MACOSX_PACKAGE_LOCATION "Resources")

    set_source_files_properties(${ASSETS} PROPERTIES MACOSX_PACKAGE_LOCATION Resources/assets)
    set(MACOSX_BUNDLE_BUNDLE_NAME "Jammer's Audio Programming")

    add_executable(Jammer MACOSX_BUNDLE ${SOURCES} ${ASSETS} ${ICON})

    set_target_properties(Jammer PROPERTIES
        MACOSX_BUNDLE_ICON_FILE icon.icns
        RESOURCE ${ICON}
    )

else()
    add_executable(Jammer WIN32 ${SOURCES} ${ASSETS})
    configure_file(
        ${CMAKE_CURRENT_SOURCE_DIR}/assets/jammer.desktop.in
        ${CMAKE_CURRENT_BINARY_DIR}/jammer.desktop
    )
    list(APPEND ASSETS
        ${CMAKE_CURRENT_SOURCE_DIR}/assets/icon.png
        ${CMAKE_CURRENT_BINARY_DIR}/jammer.desktop
    )
    file(COPY ${ASSETS} DESTINATION "assets/")

endif()

include_directories(
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/
    ${CMAKE_CURRENT_SOURCE_DIR}/libraries/libsoundio/
)

get_directory_property(LIBSOUNDIO_LIBS DIRECTORY libraries/libsoundio DEFINITION LIBSOUNDIO_LIBS)

target_link_libraries(Jammer libsoundio_static ${LIBSOUNDIO_LIBS})
