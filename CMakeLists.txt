include(ExternalProject)

add_library(luajit INTERFACE)

ExternalProject_Add(luajit-project
    BUILD_ALWAYS true
    BUILD_BYPRODUCTS <INSTALL_DIR>/usr/local/lib/libluajit-5.1.a
    BUILD_COMMAND make
    BUILD_IN_SOURCE true
    CONFIGURE_COMMAND ""
    INSTALL_COMMAND make install DESTDIR=<INSTALL_DIR>
    SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}"
)

ExternalProject_Get_Property(luajit-project INSTALL_DIR)
add_dependencies(luajit luajit-project)
target_include_directories(luajit INTERFACE "${INSTALL_DIR}/usr/local/include/luajit-2.1")
target_link_libraries(luajit INTERFACE "-Wl,--whole-archive" "${INSTALL_DIR}/usr/local/lib/libluajit-5.1.a" "-Wl,--no-whole-archive")
