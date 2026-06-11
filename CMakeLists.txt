
project(imgui)


include(FetchContent)

FetchContent_Declare(SDL3
    GIT_REPOSITORY
        https://github.com/bostick/box2d.git
    GIT_TAG
        milestone-7-branch
    GIT_SHALLOW 1
    GIT_PROGRESS 1
)
FetchContent_MakeAvailable(SDL3)

FetchContent_Declare(glm
    GIT_REPOSITORY
        https://github.com/bostick/glm.git
    GIT_TAG
        1.0.3
    GIT_SHALLOW 1
    GIT_PROGRESS 1
)
FetchContent_MakeAvailable(glm)

FetchContent_Declare(common
    GIT_REPOSITORY
        https://github.com/bostick/common.git
    GIT_TAG
        b0.7
    GIT_SHALLOW 1
    GIT_PROGRESS 1
)
FetchContent_MakeAvailable(common)


add_library(imgui-lib STATIC
    imgui.cpp
    imgui_draw.cpp
    imgui_widgets.cpp
    imgui_tables.cpp
    backends/imgui_impl_opengl3.cpp
    backends/imgui_impl_sdl3.cpp
)

target_include_directories(imgui-lib
    PUBLIC
        .
        ./backends
)


target_link_libraries(imgui-lib
    PRIVATE
        SDL3::SDL3-static
        GLESv3
        glm::glm
        common-lib
)


#
# Set up warnings
#
# https://www.foonathan.net/2018/10/cmake-warnings/
#
target_compile_options(imgui-lib
    PRIVATE
        -Wall -Wextra -pedantic -Werror -Wconversion -Wsign-conversion
)

set_target_properties(imgui-lib
    PROPERTIES
        CXX_STANDARD 20
        CXX_STANDARD_REQUIRED ON
        CXX_EXTENSIONS NO
        POSITION_INDEPENDENT_CODE ON
)


if(SANITIZE)
    if(${SANITIZE} STREQUAL "ubsan")
        target_compile_options(imgui-lib
            PRIVATE
                -fsanitize=undefined -fno-sanitize-recover=undefined
        )
        target_link_options(imgui-lib
            PRIVATE
                -fsanitize=undefined -fno-sanitize-recover=undefined
        )
    endif()
endif()
















