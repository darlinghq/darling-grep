project(grep)

cmake_minimum_required(VERSION 2.4.0)

if(COMMAND cmake_policy)
	cmake_policy(SET CMP0003 NEW)
endif(COMMAND cmake_policy)

include(darling_exe)
add_definitions(-DHAVE_CONFIG_H)

# Hide warnings
add_definitions(
	-Wno-pointer-sign
	-Wno-shift-negative-value
	-Wno-format-security
	-Wno-implicit-function-declaration
	-Wno-format
)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -U_POSIX_C_SOURCE -D__DARWIN_UNIX03 -nostdinc -fblocks -fPIC -ggdb -O0")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -include ${CMAKE_CURRENT_SOURCE_DIR}/grep/config.h")

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/grep)
include_directories(BEFORE ${CMAKE_CURRENT_SOURCE_DIR}/grep/lib)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/FreeBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/include/NetBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/gen)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/darwin)
include_directories(${DARLING_TOP_DIRECTORY}/src/libc/stdtime/FreeBSD)
include_directories(${DARLING_TOP_DIRECTORY}/src/launchd/liblaunch)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/libdispatch)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/zlib)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/bzip2)
include_directories(${DARLING_TOP_DIRECTORY}/src/ncurses/include)
include_directories(${DARLING_TOP_DIRECTORY}/src/external/pcre/pcre)

include(InstallSymlink)
set(BINARY_PACKAGING_MODE ON)

set(grep_sources
	grep/src/dfa.c
	grep/src/grep.c
	grep/src/grepmat.c
	grep/src/kwset.c
	grep/src/search.c

	grep/lib/alloca.c
	#grep/lib/atexit.c
	grep/lib/closeout.c
	grep/lib/error.c
	grep/lib/exclude.c
	grep/lib/fnmatch.c
	grep/lib/getopt1.c
	grep/lib/getopt.c
	grep/lib/hard-locale.c
	grep/lib/isdir.c
	grep/lib/malloc.c
	grep/lib/memchr.c
	grep/lib/obstack.c
	grep/lib/quotearg.c
	grep/lib/realloc.c
	grep/lib/regex.c
	grep/lib/savedir.c
	grep/lib/stpcpy.c
	grep/lib/strtol.c
	grep/lib/strtoul.c
	grep/lib/strtoull.c
	grep/lib/strtoumax.c
	grep/lib/xmalloc.c
	grep/lib/xstrtol.c
	grep/lib/xstrtoumax.c

	)

add_darling_executable(grep ${grep_sources})
target_link_libraries(grep pcre)

install(TARGETS grep
	DESTINATION libexec/darling/bin)

install(FILES grep.1 egrep.1 fgrep.1 DESTINATION libexec/darling/usr/share/man/man1)
InstallSymlink("grep" "${CMAKE_INSTALL_PREFIX}/libexec/darling/bin/egrep")
InstallSymlink("grep" "${CMAKE_INSTALL_PREFIX}/libexec/darling/bin/fgrep")
