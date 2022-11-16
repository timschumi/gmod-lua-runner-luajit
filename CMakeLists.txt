include(ExternalProject)

add_library(luajit INTERFACE)

set(LUAJIT_LIBRARY libluajit.a)
set(LUAJIT_OUTPUT_FILES src/${LUAJIT_LIBRARY} src/lauxlib.h src/luaconf.h src/lua.h src/lua.hpp src/luajit.h src/lualib.h)

ExternalProject_Add(luajit-project
    BUILD_ALWAYS true
    BUILD_BYPRODUCTS <INSTALL_DIR>/${LUAJIT_LIBRARY}
    BUILD_COMMAND make -C src ${LUAJIT_TARGET}
    BUILD_IN_SOURCE true
    CONFIGURE_COMMAND ""
    INSTALL_COMMAND ${CMAKE_COMMAND} -E copy_if_different ${LUAJIT_OUTPUT_FILES} <INSTALL_DIR>
    SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}"
)

ExternalProject_Get_Property(luajit-project INSTALL_DIR)
add_dependencies(luajit luajit-project)
target_include_directories(luajit INTERFACE "${INSTALL_DIR}")
target_link_libraries(luajit INTERFACE "-Wl,--whole-archive" "${INSTALL_DIR}/${LUAJIT_LIBRARY}" "-Wl,--no-whole-archive")
