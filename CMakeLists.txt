cmake_minimum_required (VERSION 2.6)
## Project setup
project (Gone)
set (Gone_VERSION_MAJOR 0)
set (Gone_VERSION_MINOR 2)
set (CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/Modules")
set ( CMAKE_VERBOSE_MAKEFILE OFF )
set ( CMAKE_BUILD_TYPE Debug )
# Warn about everything. Beautiful code doesn't warn.
set (CMAKE_SHARED_LINKER_FLAGS ${CMAKE_SHARED_LINKER_FLAGS} "-Wl,-lprofiler")
set (CMAKE_CXX_FLAGS "-Wall")

include (OptimizeForArchitecture)
OptimizeForArchitecture()
## Packages
find_package (Perftools REQUIRED)
find_package (Readline REQUIRED)

# Documentation - make doc
add_subdirectory(docs)

# Tests - make test
ENABLE_TESTING()
add_test(TestGoneTest gone_test)
add_test(TestGoneGame gone_game)
add_test(TestGoneSgfTest gone_sgf_test)

# Packaging
INCLUDE(CPack)

## Includes and libraries
include_directories ("include")
include_directories (${GOOGLE_PERFTOOLS_INCLUDE_DIR})
include_directories (${READLINE_INCLUDE_DIR})
add_library (board src/lib/board.cpp)
add_library (move src/lib/move.cpp)
add_library (basicplayer src/lib/basicplayer.cpp)
add_library (game src/lib/game.cpp)
add_library (sgf src/lib/sgf.cpp)

link_directories (${Perftools_TCMALLOC_LIBRARY} ${Perftools_PROFILER_LIBRARY})

## Build our executable
add_executable (gone_test src/bin/test.cpp)
target_link_libraries (gone_test ${Perftools_TCMALLOC_LIBRARY} ${Perftools_PROFILER_LIBRARY} game board basicplayer move)

add_executable (gone_game src/bin/game.cpp)
target_link_libraries (gone_game ${Perftools_TCMALLOC_LIBRARY} ${Perftools_PROFILER_LIBRARY} game board basicplayer move)

add_executable (gone_sgf_test src/bin/sgf_test.cpp)
target_link_libraries (gone_sgf_test ${Perftools_TCMALLOC_LIBRARY} ${Perftools_PROFILER_LIBRARY} sgf game board basicplayer move)

add_executable (gone src/bin/gtp.cpp)
target_link_libraries (gone ${Perftools_TCMALLOC_LIBRARY} ${READLINE_LIBRARY} ${Perftools_PROFILER_LIBRARY} sgf game board basicplayer move)