# Minimal CMake wrapper for Dear ImGui (DX11 + Win32 backend) with FreeType

cmake_minimum_required(VERSION 3.20)

set(IMGUI_SRC_DIR ${CMAKE_CURRENT_SOURCE_DIR})

add_library(imgui STATIC
    ${IMGUI_SRC_DIR}/imgui.cpp
    ${IMGUI_SRC_DIR}/imgui_demo.cpp
    ${IMGUI_SRC_DIR}/imgui_draw.cpp
    ${IMGUI_SRC_DIR}/imgui_tables.cpp
    ${IMGUI_SRC_DIR}/imgui_widgets.cpp

    # Backends
    ${IMGUI_SRC_DIR}/backends/imgui_impl_dx11.cpp
    ${IMGUI_SRC_DIR}/backends/imgui_impl_win32.cpp

    # FreeType rasterizer
    ${IMGUI_SRC_DIR}/misc/freetype/imgui_freetype.cpp
)

target_include_directories(imgui
    PUBLIC
        ${IMGUI_SRC_DIR}
        ${IMGUI_SRC_DIR}/backends
        ${IMGUI_SRC_DIR}/misc/freetype
        # FreeType headers (sibling directory)
        ${CMAKE_CURRENT_LIST_DIR}/../freetype/include
)

# Enable FreeType path in ImGui and 32-bit code points for overlay text.
target_compile_definitions(imgui PUBLIC
    IMGUI_ENABLE_FREETYPE
    IMGUI_USE_WCHAR32
)

# Link system and third-party libs
# FREETYPE_LIB_PATH is defined in the parent CMakeLists.txt
if(NOT DEFINED FREETYPE_LIB_PATH)
    message(FATAL_ERROR "FREETYPE_LIB_PATH is not defined in parent scope")
endif()

target_link_libraries(imgui
    PUBLIC
        ${FREETYPE_LIB_PATH}
        d3d11
        dxgi
        imm32
)

# Windows specific requirements
if(WIN32)
    target_compile_definitions(imgui PUBLIC IMGUI_IMPL_WIN32_DISABLE_GAMEPAD)
endif()

set_target_properties(imgui PROPERTIES
    CXX_STANDARD 23
    CXX_STANDARD_REQUIRED YES
)


