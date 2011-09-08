cmake_minimum_required(VERSION 2.8)
PROJECT(leveldb)

include(CheckIncludeFile)
include(FindBoost) # for Windows...

# version 

ENABLE_TESTING()

include_directories(${CMAKE_CURRENT_SOURCE_DIR} include)

# OS detection
if(WIN32)
    find_package(Boost)
    # We use "DIR"(cached) instead of "DIRS" here. for GURU.
    include_directories(${Boost_INCLUDE_DIR})
    add_definitions(-DLEVELDB_PLATFORM_WINDOWS -DOS_WIN -DWIN32_LEAN_AND_MEAN)
    if(MSVC)
        add_definitions(-DCOMPILER_MSVC)
    endif()
    set(PORT_SRCS port/port_win.cc util/env_boost.cc)
    set(CIF_SRCS)
elseif(CMAKE_SYSTEM_NAME STREQUAL "FreeBSD")
    add_definitions(-DLEVELDB_PLATFORM_POSIX -DOS_FREEBSD)
    set(PORT_SRCS port/port_posix.cc util/env_posix.cc)
    set(CIF_SRCS db/c.cc)
elseif(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    add_definitions(-DLEVELDB_PLATFORM_POSIX -DOS_LINUX)
    # link pthread
    set(PORT_SRCS port/port_posix.cc util/env_posix.cc)
    set(CIF_SRCS db/c.cc)
elseif(APPLE)
    add_definitions(-DLEVELDB_PLATFORM_POSIX -DOS_MACOSX)
    set(PORT_SRCS port/port_posix.cc util/env_posix.cc)
    set(CIF_SRCS db/c.cc)
endif()

# FIXME: check cstdatomic here..

set(SRCS 
    db/builder.cc
    db/db_impl.cc
    db/db_iter.cc
    db/filename.cc
    db/dbformat.cc
    db/log_reader.cc
    db/log_writer.cc
    db/memtable.cc
    db/repair.cc
    db/table_cache.cc
    db/version_edit.cc
    db/version_set.cc
    db/write_batch.cc
    table/block.cc
    table/block_builder.cc
    table/format.cc
    table/iterator.cc
    table/merger.cc
    table/table.cc
    table/table_builder.cc
    table/two_level_iterator.cc
    util/arena.cc
    util/cache.cc
    util/coding.cc
    util/comparator.cc
    util/crc32c.cc
    util/hash.cc
    util/histogram.cc
    util/logging.cc
    util/options.cc
    util/status.cc
    ${PORT_SRCS}
    ${CIF_SRCS}
    )

include_directories(${CMAKE_CURRENT_BINARY_DIR})

# CHECK_INCLUDE_FILE(stdint.h HAVE_STDINT_H)


add_library(leveldb ${SRCS})

