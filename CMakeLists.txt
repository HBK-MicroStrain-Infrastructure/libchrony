cmake_minimum_required(VERSION 3.10)
project(libchrony VERSION 0.2 LANGUAGES C)

set(CMAKE_C_STANDARD 11)
set(CMAKE_C_STANDARD_REQUIRED ON)

add_compile_options(-Wall -g)

set(chrony_sources client.c message.c socket.c)
set(chrony_public_headers chrony.h)

add_library(chrony SHARED ${chrony_sources})
target_include_directories(chrony PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
target_link_libraries(chrony PUBLIC m)
set_target_properties(chrony PROPERTIES
    VERSION 0.2.0
    SOVERSION 0
)

add_executable(example-reports EXCLUDE_FROM_ALL example-reports.c ${chrony_sources})
target_link_libraries(example-reports PRIVATE m)

add_executable(fuzz EXCLUDE_FROM_ALL fuzz.c ${chrony_sources})

include(GNUInstallDirs)

install(TARGETS chrony LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(FILES ${chrony_public_headers} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

configure_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/libchrony.pc.in"
    "${CMAKE_CURRENT_BINARY_DIR}/libchrony.pc"
    @ONLY
)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/libchrony.pc"
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
)
