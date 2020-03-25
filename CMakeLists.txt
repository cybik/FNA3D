CMAKE_MINIMUM_REQUIRED(VERSION 3.1)
PROJECT(FNA3D)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fpic -fPIC -g -O3 -Wall -pedantic")

option(FNA3D_DRIVER_OPENGL "Build OpenGL driver" OFF)

find_library(SDL2 NAME SDL2)
add_definitions(
	-DMOJOSHADER_NO_VERSION_INCLUDE
	-DMOJOSHADER_EFFECT_SUPPORT
	-DMOJOSHADER_DEPTH_CLIPPING
	-DMOJOSHADER_FLIP_RENDERTARGET
	-DMOJOSHADER_XNA4_VERTEX_TEXTURES
	-DSUPPORT_PROFILE_ARB1=0
	-DSUPPORT_PROFILE_ARB1_NV=0
	-DSUPPORT_PROFILE_BYTECODE=0 
    -DSUPPORT_PROFILE_D3D=0
    -DSUPPORT_PROFILE_METAL=0
    -DWINDOWS_TARGET=1
)

if(FNA3D_DRIVER_OPENGL)
    add_definitions(-DFNA3D_DRIVER_OPENGL)
endif()

add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/MojoShader" "mojoshader")

include_directories(
    "${CMAKE_CURRENT_LIST_DIR}/MojoShader"
    "${CMAKE_CURRENT_LIST_DIR}/include"
    ${SDL2_INCLUDE_DIR}
)

LIST(APPEND
    FNA3DSRC
        "${CMAKE_CURRENT_LIST_DIR}/src/FNA3D.c"
        "${CMAKE_CURRENT_LIST_DIR}/src/FNA3D_Driver_OpenGL.c"
)

add_library(
    FNA3D
        SHARED
            ${FNA3DSRC}
)
target_link_libraries(
    FNA3D
        mojoshader
        SDL2
)

add_executable(initGL.test tests/initGL.c)
target_link_libraries(
    initGL.test
        FNA3D
        SDL2
)
