cmake_minimum_required(VERSION 3.15)
project(mirco_lua LANGUAGES C)

option(MIRCO_LUA_BUILD_EXECUTABLE   "" ON)

set(MIRCO_LUA_INCLUDE_DIRS          ${CMAKE_CURRENT_SOURCE_DIR}/include/mirco_lua)
set(MIRCO_LUA_CORE_INCLUDE_DIRS     ${MIRCO_LUA_INCLUDE_DIRS}/core)
set(MIRCO_LUA_PARSER_INCLUDE_DIRS   ${MIRCO_LUA_INCLUDE_DIRS}/parser)
set(MIRCO_LUA_LIB_INCLUDE_DIRS      ${MIRCO_LUA_INCLUDE_DIRS}/lib)
set(MIRCO_LUA_EXTRA_INCLUDE_DIRS    ${MIRCO_LUA_INCLUDE_DIRS}/Extra)
set(MIRCO_LUA_SRC_DIRS              ${CMAKE_CURRENT_SOURCE_DIR}/src)

file(GLOB MIRCO_LUA_CORE_SRCS       ${MIRCO_LUA_SRC_DIRS}/core/*.c)
file(GLOB MIRCO_LUA_PARSER_SRCS     ${MIRCO_LUA_SRC_DIRS}/parser/*.c)
file(GLOB MIRCO_LUA_LIB_SRCS        ${MIRCO_LUA_SRC_DIRS}/lib/*.c)
file(GLOB MIRCO_LUA_EXTRA_SRCS      ${MIRCO_LUA_SRC_DIRS}/extra/*.c)


add_library(mirco_lua_core STATIC ${MIRCO_LUA_CORE_SRCS})
target_include_directories(mirco_lua_core PRIVATE
    ${MIRCO_LUA_CORE_INCLUDE_DIRS}
    ${MIRCO_LUA_PARSER_INCLUDE_DIRS}
    ${CMAKE_CURRENT_SOURCE_DIR}
)


add_library(mirco_lua_parser STATIC ${MIRCO_LUA_PARSER_SRCS})
target_include_directories(mirco_lua_parser PRIVATE
    ${MIRCO_LUA_PARSER_INCLUDE_DIRS}
    ${MIRCO_LUA_CORE_INCLUDE_DIRS}
)
target_link_libraries(mirco_lua_parser PUBLIC mirco_lua_core)


add_library(mirco_lua_extra STATIC ${MIRCO_LUA_EXTRA_SRCS})
target_include_directories(mirco_lua_extra PRIVATE
    ${MIRCO_LUA_CORE_INCLUDE_DIRS}
    ${MIRCO_LUA_PARSER_INCLUDE_DIRS}
    ${MIRCO_LUA_LIB_INCLUDE_DIRS}
    ${MIRCO_LUA_EXTRA_INCLUDE_DIRS}
)
target_link_libraries(mirco_lua_extra PUBLIC mirco_lua_core)


add_library(mirco_lua_lib STATIC ${MIRCO_LUA_LIB_SRCS})
target_include_directories(mirco_lua_lib PRIVATE
    ${MIRCO_LUA_CORE_INCLUDE_DIRS}
    ${MIRCO_LUA_LIB_INCLUDE_DIRS}
    ${MIRCO_LUA_EXTRA_INCLUDE_DIRS}
)
target_link_libraries(mirco_lua_lib PUBLIC mirco_lua_core)

if(${MIRCO_LUA_BUILD_EXECUTABLE})
    add_executable(mirco_lua ${MIRCO_LUA_SRC_DIRS}/lua.c)
    target_include_directories(mirco_lua PRIVATE 
        ${MIRCO_LUA_CORE_INCLUDE_DIRS}
        ${MIRCO_LUA_PARSER_INCLUDE_DIRS}
        ${MIRCO_LUA_LIB_INCLUDE_DIRS}
        ${MIRCO_LUA_EXTRA_INCLUDE_DIRS}
    )
    target_link_libraries(mirco_lua PRIVATE
        mirco_lua_core 
        mirco_lua_parser
        mirco_lua_lib
        mirco_lua_extra
    )
endif()