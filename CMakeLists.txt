project(sqlite3)

enable_language(C)

include(ExternalProject)

option(SQLITE3_WITH_SHELL "build sqlite3 executable" OFF)
option(SQLITE3_STATIC_LIB "build sqlite3 static library" ON)
option(SQLITE3_SHARED_LIB "build sqlite3 shared library" OFF)
option(SQLITE3_USE_SHARED_CRT "use shared crt library" OFF)

if (MSVC)
if (SQLITE3_USE_SHARED_CRT)
       foreach(flag_var
                CMAKE_CXX_FLAGS_DEBUG
                CMAKE_CXX_FLAGS_RELEASE
                CMAKE_CXX_FLAGS_MINSIZEREL
                CMAKE_CXX_FLAGS_RELWITHDEBINFO
                CMAKE_C_FLAGS_DEBUG
                CMAKE_C_FLAGS_RELEASE
                CMAKE_C_FLAGS_MINSIZEREL
                CMAKE_C_FLAGS_RELWITHDEBINFO
                )
            string(REPLACE "/MD" "/MT" ${flag_var} "${${flag_var}}")
        endforeach()
#else(SQLITE3_USE_SHARED_CRT)
endif(SQLITE3_USE_SHARED_CRT)
endif(MSVC)
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
     
install(TARGETS sqlite3
        EXPORT sqlite3
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include
        )
export(TARGETS sqlite3 FILE sqlite3.cmake)
export(TARGETS sqlite3-shared FILE sqlite3-shared.cmake)
export(PACKAGE sqlite3)
