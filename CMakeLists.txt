##############################################
#   CMake
##############################################
project(ybatch C)
cmake_minimum_required(VERSION 2.8)
include(CheckIncludeFiles)
include(CheckSymbolExists)

set(CMAKE_INSTALL_PREFIX /yuneta/development/output)

set(BIN_DEST_DIR ${CMAKE_INSTALL_PREFIX}/agent)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -std=c99")

if(CMAKE_BUILD_TYPE MATCHES Debug)
  add_definitions(-DDEBUG)
  option(SHOWNOTES "Show preprocessor notes" OFF)

  if(CMAKE_COMPILER_IS_GNUCC)
    # GCC specific debug options
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -O0 -g3 -ggdb3 -gdwarf-2")
    set(AVOID_VERSION -avoid-version)
  endif(CMAKE_COMPILER_IS_GNUCC)
endif(CMAKE_BUILD_TYPE MATCHES Debug)

add_definitions(-D_GNU_SOURCE)
add_definitions(-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64)

check_include_files(regex.h HAVE_REGEX_H)
if(HAVE_REGEX_H)
    add_definitions(-DHAVE_REGEX_H)
else(HAVE_REGEX_H)
    message("***  WARNING: GNU C regex library not found.")
endif(HAVE_REGEX_H)

include_directories(BEFORE /yuneta/development/output/include)

##############################################
#   Source
##############################################

SET (YUNO_SRCS
    src/main.c
    src/yuno_ybatch.c
    src/c_ybatch.c
)
SET (YUNO_HDRS
    src/yuno_ybatch.h
    src/c_ybatch.h
)

##############################################
#   yuno
##############################################
ADD_EXECUTABLE(ybatch ${YUNO_SRCS} ${YUNO_HDRS})

if(CMAKE_BUILD_TYPE MATCHES Debug)
TARGET_LINK_LIBRARIES(ybatch
    /yuneta/development/output/lib/libyuneta-core.a
    /yuneta/development/output/lib/libginsfsm.a
    /yuneta/development/output/lib/libghelpersd.a
    /yuneta/development/output/lib/libuv.a
    /yuneta/development/output/lib/libjansson.a
    /yuneta/development/output/lib/libunwind.a
    /yuneta/development/output/lib/libpcre2-8.a

    pthread     # used by libuv
    lzma        # used by libunwind

)
else(CMAKE_BUILD_TYPE MATCHES Debug)
TARGET_LINK_LIBRARIES(ybatch
    /yuneta/development/output/lib/libyuneta-core.a
    /yuneta/development/output/lib/libginsfsm.a
    /yuneta/development/output/lib/libghelpers.a
    /yuneta/development/output/lib/libuv.a
    /yuneta/development/output/lib/libjansson.a
    /yuneta/development/output/lib/libunwind.a
    /yuneta/development/output/lib/libpcre2-8.a

    pthread     # used by libuv
    lzma        # used by libunwind

)
endif(CMAKE_BUILD_TYPE MATCHES Debug)

##############################################
#   Installation
##############################################

install(
    TARGETS ybatch
    PERMISSIONS
    OWNER_READ OWNER_WRITE OWNER_EXECUTE
    GROUP_READ GROUP_WRITE GROUP_EXECUTE
    WORLD_READ WORLD_EXECUTE
    DESTINATION ${BIN_DEST_DIR}
)
