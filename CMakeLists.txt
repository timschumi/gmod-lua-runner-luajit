include(ExternalProject)

add_library(luajit INTERFACE)

set(LUAJIT_CC "${CMAKE_C_COMPILER}")
set(LUAJIT_HOST_CC "gcc")
if("${CMAKE_HOST_SYSTEM_PROCESSOR}" STREQUAL "x86_64" AND ${CMAKE_SIZEOF_VOID_P} LESS 8)
    set(LUAJIT_HOST_CC "${LUAJIT_HOST_CC} -m32")
endif()
set(LUAJIT_TARGET_SYS "${CMAKE_SYSTEM_NAME}")
set(LUAJIT_LIBRARY libluajit.a)
set(LUAJIT_OUTPUT_FILES src/${LUAJIT_LIBRARY} src/lauxlib.h src/luaconf.h src/lua.h src/lua.hpp src/luajit.h src/lualib.h)
set(LUAJIT_SETTINGS TARGET_SYS=${LUAJIT_TARGET_SYS} CC=${LUAJIT_CC} HOST_CC=${LUAJIT_HOST_CC})

ExternalProject_Add(luajit-project
    BUILD_ALWAYS true
    BUILD_BYPRODUCTS <INSTALL_DIR>/${LUAJIT_LIBRARY}
    BUILD_COMMAND make -C src ${LUAJIT_SETTINGS} ${LUAJIT_TARGET}
    BUILD_IN_SOURCE true
    CONFIGURE_COMMAND ""
    DOWNLOAD_COMMAND rsync -a ${CMAKE_CURRENT_SOURCE_DIR}/ <SOURCE_DIR>
    INSTALL_COMMAND ${CMAKE_COMMAND} -E copy_if_different ${LUAJIT_OUTPUT_FILES} <INSTALL_DIR>
    UPDATE_COMMAND rsync -a ${CMAKE_CURRENT_SOURCE_DIR}/ <SOURCE_DIR>
)

ExternalProject_Get_Property(luajit-project INSTALL_DIR)
add_dependencies(luajit luajit-project)
target_include_directories(luajit INTERFACE "${INSTALL_DIR}")
target_link_libraries(luajit INTERFACE "-Wl,--whole-archive" "${INSTALL_DIR}/${LUAJIT_LIBRARY}" "-Wl,--no-whole-archive")
