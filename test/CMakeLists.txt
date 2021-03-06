# automatically enable catch2 to generate ctest targets
if(CONAN_CATCH2_ROOT_DEBUG)
    include(${CONAN_CATCH2_ROOT_DEBUG}/lib/cmake/Catch2/Catch.cmake)
else()
    include(${CONAN_CATCH2_ROOT}/lib/cmake/Catch2/Catch.cmake)
endif()


add_library(catch_main STATIC catch_main.cpp)
target_link_libraries(catch_main PUBLIC CONAN_PKG::catch2)
target_link_libraries(catch_main PRIVATE project_options)

add_executable(tests
        tests.cpp
        "${CMAKE_SOURCE_DIR}/src/private/SpriteHolder.cpp"
        "${CMAKE_SOURCE_DIR}/src/private/Enums.cpp"
        )
target_link_libraries(tests PRIVATE
        project_warnings
        project_options
        catch_main
        CONAN_PKG::imgui-sfml
        CONAN_PKG::spdlog
        )
target_include_directories(tests PUBLIC "${PROJECT_BINARY_DIR}" PUBLIC "${CMAKE_SOURCE_DIR}/src") #for access config.h and src directory

# automatically discover tests that are defined in catch based test files you
# can modify the unittests. TEST_PREFIX to whatever you want, or use different
# for different binaries
catch_discover_tests(
        tests
        TEST_PREFIX
        "unittests."
        EXTRA_ARGS
        -s
        --reporter=xml
        --out=tests.xml)