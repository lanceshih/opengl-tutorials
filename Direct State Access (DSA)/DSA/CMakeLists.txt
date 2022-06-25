cmake_minimum_required(VERSION 3.19)

get_filename_component(ProjectName ${CMAKE_CURRENT_SOURCE_DIR} NAME)
string(REGEX REPLACE "[^a-zA-Z0-9]+" "_" ProjectName ${ProjectName})
project(${ProjectName})

set(CMAKE_CXX_STANDARD 17)
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")

set(SOURCE main.cpp glad.c stb.cpp)

add_executable(${ProjectName} ${SOURCE})

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/libraries/include)

if (WIN32)
    if (CMAKE_SIZEOF_VOID_P EQUAL 8) # 64 bits
        target_link_libraries(${ProjectName} "${PROJECT_SOURCE_DIR}/libraries/lib/glfw3.lib")
    elseif (CMAKE_SIZEOF_VOID_P EQUAL 4) # 32 bits
        message(WARNING "libaries won't link in x86")
    endif ()
elseif (UNIX)
    find_package(glfw3 REQUIRED)
    target_link_libraries(${ProjectName} glfw)
endif (WIN32)

add_custom_command(
        TARGET ${ProjectName} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy
        ${PROJECT_SOURCE_DIR}/hamster.png $<TARGET_FILE_DIR:${ProjectName}>/hamster.png
        VERBATIM
)