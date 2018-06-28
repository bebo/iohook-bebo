cmake_minimum_required(VERSION 3.11)

# Name of the project (will be the name of the plugin)
project(iohook)
include_directories(${CMAKE_JS_INC} ../nan)

set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} /MT /Zi")
set(CMAKE_SHARED_LINKER_FLAGS_RELEASE "${CMAKE_SHARED_LINKER_FLAGS_RELEASE} /DEBUG /OPT:REF /OPT:ICF")
set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} /MTd")

# Build a shared library named after the project from the files in `src/`
file(GLOB SOURCE_FILES "src/*.cc" "src/*.h")
add_library(${PROJECT_NAME} SHARED ${SOURCE_FILES})

file(GLOB UIHOOK "lib/uiohook.lib" "lib/uihook.dll")
# LINK_DIRECTORIES("${CMAKE_CURRENT_SOURCE_DIR}/lib")

# Gives our library file a .node extension without any "lib" prefix
set_target_properties(${PROJECT_NAME} PROPERTIES PREFIX "" SUFFIX ".node")

# Essential include files to build a node addon,
# You should add this line in every CMake.js based project
target_include_directories(${PROJECT_NAME} PRIVATE ${CMAKE_JS_INC})
# target_include_directories(${PROJECT_NAME} PRIVATE ${UIHOOK})
target_link_libraries(${PROJECT_NAME} ${CMAKE_JS_LIB} ${UIHOOK})

add_custom_command(
  TARGET ${PROJECT_NAME} POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy_if_different
    "${PROJECT_SOURCE_DIR}/lib/uiohook.dll"
    $<TARGET_FILE_DIR:${PROJECT_NAME}>
)

# if("${CMAKE_SYSTEM_NAME}" STREQUAL "Linux")
#   target_link_libraries(${PROJECT_NAME} ${CMAKE_JS_LIB} "uiohook" "xkbfile" "xkbcommon-x11" "xkbcommon" "X11-xcb" "xcb" "Xinerama" "Xt" "Xtst" "X11")
# endif()

# if(CMAKE_SYSTEM_NAME MATCHES "(Darwin)")
#   find_library(FRAMEWORK_IOKIT IOKit)
#   find_library(FRAMEWORK_Carbon Carbon)
#   target_link_libraries(${PROJECT_NAME} ${FRAMEWORK_IOKIT} ${FRAMEWORK_Carbon})
# endif()
