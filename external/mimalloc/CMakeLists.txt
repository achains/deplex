include(FetchContent)

FetchContent_Declare(
        mimalloc
        GIT_REPOSITORY https://github.com/microsoft/mimalloc.git
        GIT_TAG v2.1.2
)

FetchContent_MakeAvailable(mimalloc)