cmake_minimum_required(VERSION 3.22)

set(PATCH_VERSION "1" CACHE INTERNAL "Patch version")
set(PROJECT_VESRION 0.0.${PATCH_VERSION})

project(helloworld VERSION ${PROJECT_VESRION})

configure_file(version.h.in version.h)

add_executable(helloworld_cli main.cpp)
add_library(helloworld lib.cpp)

set_target_properties(helloworld_cli helloworld PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
)

target_compile_options(helloworld_cli PRIVATE
   -Wall -Wextra -pedantic -Werror
)
target_compile_options(helloworld PRIVATE
  -Wall -Wextra -pedantic -Werror
)

target_include_directories(helloworld
    PRIVATE "${CMAKE_BINARY_DIR}"
)

target_link_libraries(helloworld_cli PRIVATE
    helloworld
)

option(WITH_GOOGLE_TEST "Whether to build Google test" ON)

if (WITH_GOOGLE_TEST)
  enable_testing()

  find_package(GTest REQUIRED)

  add_executable(
    test_version
    test_version.cpp
  )

  set_target_properties(test_version PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED ON
  )

  target_compile_options(test_version PRIVATE
    -Wall -Wextra -pedantic -Werror
  )

  target_link_libraries(
    test_version
    GTest::gtest_main
    helloworld
  )

  add_test(test_version test_version)

endif()

install(TARGETS helloworld_cli RUNTIME DESTINATION bin)

set(CPACK_GENERATOR DEB)
set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")
set(CPACK_PACKAGE_CONTACT example@example.com)
include(CPack)
