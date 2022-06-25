cmake_minimum_required(VERSION 3.23)

get_filename_component(ProjectName ${CMAKE_CURRENT_SOURCE_DIR} NAME)
string(REGEX REPLACE "[^a-zA-Z0-9]+" "_" ProjectName ${ProjectName})
project(${ProjectName})

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")

set(SOURCE Main.cpp glad.c EBO.cpp shaderClass.cpp VAO.cpp VBO.cpp stb.cpp Texture.cpp Camera.cpp Mesh.cpp Model.cpp)
set(SHADERS default.vert default.frag default.geom framebuffer.vert framebuffer.frag blur.frag)

add_executable(${ProjectName} ${SOURCE})

include_directories(${PROJECT_SOURCE_DIR})
include_directories(${PROJECT_SOURCE_DIR}/Libraries/include)

if (WIN32)
    if (CMAKE_SIZEOF_VOID_P EQUAL 8) # 64 bits
        target_link_libraries(${ProjectName} "${PROJECT_SOURCE_DIR}/Libraries/lib/glfw3.lib")
    elseif (CMAKE_SIZEOF_VOID_P EQUAL 4) # 32 bits
        message(WARNING "libaries won't link in x86")
    endif ()
elseif (UNIX)
    find_package(glfw3 REQUIRED)
    target_link_libraries(${ProjectName} glfw)
endif (WIN32)

foreach (file ${SHADERS})
    add_custom_command(
            TARGET ${ProjectName} POST_BUILD
            COMMAND ${CMAKE_COMMAND} -E copy_if_different
            ${PROJECT_SOURCE_DIR}/${file} $<TARGET_FILE_DIR:${ProjectName}>/${file}
            VERBATIM
    )
endforeach (file)

add_custom_command(
        TARGET ${ProjectName} POST_BUILD
        COMMAND ${CMAKE_COMMAND} -E copy_directory
        ${CMAKE_HOME_DIRECTORY}/Resources ${CMAKE_BINARY_DIR}/Resources
        VERBATIM
)