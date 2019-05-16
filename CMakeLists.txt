project(sqlite3)

include(ExternalProject)

ExternalProject_Add(sqlite3-src
       URL https://sqlite.org/2019/sqlite-amalgamation-3280000.zip
       PREFIX 3rdparty

       PATCH_COMMAND  ${CMAKE_COMMAND} -E
       copy
       ${CMAKE_CURRENT_SOURCE_DIR}/CMakeLists.txt.in
       ${CMAKE_CURRENT_BINARY_DIR}/3rdparty/src/sqlite3-src/CMakeLists.txt

       CONFIGURE_COMMAND 
       ${CMAKE_COMMAND} -DSQLITE_WITH_SHELL=${SQLITE_WITH_SHELL}:bool
                        -DSQLITE3_STATIC_LIB=${SQLITE3_STATIC_LIB}:bool
                        -DSQLITE3_SHARED_LIB=${SQLITE3_SHARED_LIB}:bool
       <SOURCE_DIR> 

       INSTALL_COMMAND ""
       )

option(SQLITE3_WITH_SHELL "build sqlite3 executable" OFF)
option(SQLITE3_STATIC_LIB "build sqlite3 static library" ON)
option(SQLITE3_SHARED_LIB "build sqlite3 shared library" OFF)
     

