cmake_minimum_required(VERSION 3.27)

set(PATCH_VERSION "1" CACHE INTERNAL "My Patch version")
set(PROJECT_VERSION 0.0.${PATCH_VERSION})

project(cpp_medium01 VERSION ${PROJECT_VERSION})

find_package(Boost COMPONENTS unit_test_framework REQUIRED)

configure_file(version.h.in version.h)

# добавил exe-файл созданный из
add_executable(helloworld_cli main.cpp)
# добавить библиотеку собранную из
add_library(cpp_medium01 lib.cpp)
add_executable(test_version test_version.cpp)

set_target_properties(helloworld_cli cpp_medium01 test_version PROPERTIES
    CXX_STANDARD 14
    CXX_STANDARD_REQUIRED ON
)
target_include_directories(cpp_medium01
        PRIVATE "${CMAKE_BINARY_DIR}"
)
set_target_properties(test_version PROPERTIES
    COMPILE_DEFINITIONS BOOST_TEST_DYN_LINK
    INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR}
)

target_link_libraries(helloworld_cli PRIVATE
    cpp_medium01
)
target_link_libraries(test_version
    ${Boost_LIBRARIES}
    cpp_medium01
)

if (MSVC)
    target_compile_options(hellowrld_cli PRIVATE
        /W4
    )
    target_compile_options(cpp_medium01 PRIVATE
        /W4
    )
    target_compile_options(test_version PRIVATE
        /W4
    )
else ()
    target_compile_options(helloworld_cli PRIVATE
        -Wall -Wextra -pedantic -Werror
    )
    target_compile_options(cpp_medium01 PRIVATE
        -Wall -Wextra -pedantic -Werror
    )
    target_compile_options(test_version PRIVATE
        -Wall -Wextra -pedantic -Werror
    )
endif()

# процедура формирования Пакета
# функционал INSTALL (без лишнего)
# создаст bin и положит туда helloworld_cli.exe
install(TARGETS helloworld_cli RUNTIME DESTINATION bin)
# встроенный генератор debian-пакетов
set(CPACK_GENERATOR DEB)

set(CPACK_PACKAGE_VERSION_MAJOR "${PROJECT_VERSION_MAJOR}")
set(CPACK_PACKAGE_VERSION_MINOR "${PROJECT_VERSION_MINOR}")
set(CPACK_PACKAGE_VERSION_PATCH "${PROJECT_VERSION_PATCH}")

set(CPACK_PACKAGE_CONTACT example@ex.com)
# генератор debian пакетов
include(CPack)
# возможность запускать тесты (с учетом платформозависимости)
enable_testing()

add_test(test_version test_version)