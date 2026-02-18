cmake_minimum_required(VERSION 3.16)
project(KonsoleGE LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# --- KonsoleGE static library ---
file(GLOB_RECURSE KONSOLEGE_SRCS ${CMAKE_CURRENT_SOURCE_DIR}/source/*.cpp)

if(WIN32)
    list(FILTER KONSOLEGE_SRCS EXCLUDE REGEX "Linux\\.cpp$")
else()
    list(FILTER KONSOLEGE_SRCS EXCLUDE REGEX "Win32\\.cpp$")
endif()

add_library(konsolege STATIC ${KONSOLEGE_SRCS})

target_include_directories(konsolege PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/source/Core
    ${CMAKE_CURRENT_SOURCE_DIR}/source/Platform
    ${CMAKE_CURRENT_SOURCE_DIR}/source/UI
    ${CMAKE_CURRENT_SOURCE_DIR}/source/Util
)
target_include_directories(konsolege SYSTEM PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}/include
    ${CMAKE_BINARY_DIR}
)

if(WIN32)
    target_link_libraries(konsolege PUBLIC winmm ole32)
elseif(APPLE)
    find_library(COREAUDIO_LIB CoreAudio REQUIRED)
    find_library(AUDIOTOOLBOX_LIB AudioToolbox REQUIRED)
    find_library(COREFOUNDATION_LIB CoreFoundation REQUIRED)
    target_link_libraries(konsolege PUBLIC
        pthread dl m
        ${COREAUDIO_LIB} ${AUDIOTOOLBOX_LIB} ${COREFOUNDATION_LIB}
    )
else()
    target_link_libraries(konsolege PUBLIC pthread dl m)
endif()
