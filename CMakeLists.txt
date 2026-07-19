
project(imgui)


include(FetchContent)


if("${LOCAL_SDL_DIR}" STREQUAL "")
    message(FATAL_ERROR "LOCAL_SDL_DIR is not set!")
endif()

FetchContent_Declare(SDL3
    # GIT_REPOSITORY
    #     https://github.com/bostick/SDL.git
    # GIT_TAG
    #     bostick-main
    # GIT_SHALLOW 1
    # GIT_PROGRESS 1
    SOURCE_DIR
        ${LOCAL_SDL_DIR}
)
FetchContent_MakeAvailable(SDL3)


if("${LOCAL_COMMON_DIR}" STREQUAL "")
    message(FATAL_ERROR "LOCAL_COMMON_DIR is not set!")
endif()

FetchContent_Declare(common
    # GIT_REPOSITORY
    #     https://github.com/bostick/common.git
    # GIT_TAG
    #     main
    # GIT_SHALLOW 1
    # GIT_PROGRESS 1
    SOURCE_DIR
        ${LOCAL_COMMON_DIR}
)
FetchContent_MakeAvailable(common)


set(SOURCES_LIB
    imgui.cpp
    imgui_draw.cpp
    imgui_widgets.cpp
    imgui_tables.cpp

    backends/imgui_impl_sdl3.cpp
    backends/imgui_impl_sdlrenderer3.cpp
)


add_library(imgui-lib STATIC
    ${SOURCES_LIB}
)


target_include_directories(imgui-lib
    PUBLIC
        .
        ./backends
)


target_link_libraries(imgui-lib
    PRIVATE
        SDL3::SDL3-static
        common-lib
)


set_target_properties(imgui-lib
    PROPERTIES
        CXX_STANDARD 20
        CXX_STANDARD_REQUIRED ON
        CXX_EXTENSIONS NO
        POSITION_INDEPENDENT_CODE ON
)


if(${CMAKE_SYSTEM_NAME} STREQUAL "iOS")

target_compile_options(imgui-lib
    PRIVATE
        -fobjc-arc
  )

endif()


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
















