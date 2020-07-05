cmake_minimum_required(VERSION 3.5)

project(conan_test LANGUAGES CXX)

string(TOLOWER ${CMAKE_BUILD_TYPE} BUILD_TYPE)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

#include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
#conan_basic_setup()

set(CONTRIB_DIR ${CMAKE_SOURCE_DIR}/contrib/build/${BUILD_TYPE})
include(${CONTRIB_DIR}/qt/conanbuildinfo.cmake)

#include(${CONTRIB_DIR}/conanbuildinfo.cmake)
conan_basic_setup(NO_OUTPUT_DIRS)

if(UNIX)
    add_definitions(-DMEDIA_BUILD_LINUX)

    set(CMAKE_FIND_LIBRARY_SUFFIXES ".a" ".so")
    set(CMAKE_C_FLAGS "\
        -Wall \
        -pthread \
        -fPIC \
        -funsigned-char \
        -rdynamic \
        ")
    set(CMAKE_C_FLAGS_DEBUG "\
        -g \
        -ggdb \
        -DDEBUG \
        --coverage \
        ")
    set(CMAKE_C_FLAGS_RELEASE "\
        -g \
        -ggdb \
        -DNDEBUG \
        -O2 \
        ")
    set(CMAKE_CXX_FLAGS "${CMAKE_C_FLAGS} \
        -Wsuggest-override \
        ")
    set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG}")
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE}")

    set(CMAKE_LINK_WHAT_YOU_USE TRUE)

endif()

set(QT5_ROOT ${CONAN_QT_ROOT})

set(CMAKE_PREFIX_PATH ${QT5_ROOT})

find_package(Qt5Core REQUIRED)
find_package(Qt5Gui REQUIRED)
find_package(Qt5Widgets REQUIRED)
find_package(Qt5Quick REQUIRED)
find_package(Qt5Qml REQUIRED)
find_package(Qt5Svg REQUIRED)

set(QT5_CORE_LIB       Qt5::Core)
set(QT5_GUI_LIB        Qt5::Gui)
set(QT5_WIDGETS_LIB    Qt5::Widgets)
set(QT5_QUICK_LIB      Qt5::Quick)
set(QT5_QML_LIB        Qt5::Qml)
set(QT5_SVG_LIB        Qt5::Svg)

if(UNIX)
    find_package(Qt5X11Extras REQUIRED)
    set(QT5_X11_EXTRAS_LIB Qt5::X11Extras)
endif()

find_package(Qt5LinguistTools REQUIRED)
set(QT5_LUPDATE_EXECUTABLE ${Qt5_LUPDATE_EXECUTABLE})

set(QMLLINT_EXECUTABLE ${QT5_ROOT}/bin/qmllint)

add_executable(conan_test main.cpp)
target_link_libraries(conan_test ${CONAN_LIBS})
