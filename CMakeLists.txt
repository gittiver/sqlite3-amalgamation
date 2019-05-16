project(sqlite3)

include(ExternalProject)

ExternalProject_Add(sqlite3-src
       URL https://sqlite.org/2019/sqlite-amalgamation-3280000.zip
       PREFIX 3rdparty

       CONFIGURE_COMMAND ""
       BUILD_COMMAND ""
       INSTALL_COMMAND ""
)

set(SQLITE3_SOURCES
  ${CMAKE_CURRENT_BINARY_DIR}/3rdparty/src/sqlite3-src/sqlite3.c
  ${CMAKE_CURRENT_BINARY_DIR}/3rdparty/src/sqlite3-src/sqlite3.h
  ${CMAKE_CURRENT_BINARY_DIR}/3rdparty/src/sqlite3-src/sqlite3ext.h
  )

set(SQLITE3_SHELL_SOURCES
  ${CMAKE_CURRENT_BINARY_DIR}/3rdparty/src/sqlite3-src/shell.c
  )

add_custom_command(
  OUTPUT ${SQLITE3_SOURCES} ${SQLITE3_SHELL_SOURCES}
  DEPENDS sqlite3-src
) 

add_library(sqlite3 STATIC ${SQLITE3_SOURCES})
add_library(sqlite3-shared SHARED ${SQLITE3_SOURCES})
set_target_properties(sqlite3-shared
        PROPERTIES OUTPUT_NAME sqlite3)

if(SQLITE3_WITH_SHELL)
  add_executable(sqlite3-bin ${SQLITE3_SHELL_SOURCES})
  target_link_libraries(sqlite3-bin sqlite3)
  set_target_properties(sqlite3-bin
        PROPERTIES OUTPUT_NAME sqlite3)
endif(SQLITE3_WITH_SHELL)

option(SQLITE3_WITH_SHELL "build sqlite3 executable" OFF)
option(SQLITE3_STATIC_LIB "build sqlite3 static library" ON)
option(SQLITE3_SHARED_LIB "build sqlite3 shared library" OFF)
     

