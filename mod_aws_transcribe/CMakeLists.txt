cmake_minimum_required(VERSION 3.18)
project(mod_aws_transcribe)

set(ENV{PKG_CONFIG_PATH} "/usr/local/freeswitch/lib/pkgconfig:/usr/local/ssl/lib/pkgconfig/:$ENV{PKG_CONFIG_PATH}")

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_SHARED_LIBRARY_PREFIX "")
set(CMAKE_C_FLAGS_DEBUG "${CMAKE_C_FLAGS_DEBUG} -g")


find_package(PkgConfig REQUIRED)
  find_package(ZLIB REQUIRED)
find_package(AWSSDK COMPONENTS transcribestreaming)

pkg_check_modules(FreeSWITCH REQUIRED IMPORTED_TARGET freeswitch)
pkg_get_variable(FS_MOD_DIR freeswitch modulesdir)
message("FreeSWITCH modules dir: ${FS_MOD_DIR}")

add_library(mod_aws_transcribe SHARED mod_aws_transcribe.c aws_transcribe_glue.cpp mod_aws_transcribe.h aws_transcribe_glue.h simple_buffer.h)
set_property(TARGET mod_aws_transcribe PROPERTY POSITION_INDEPENDENT_CODE ON)


target_include_directories(mod_aws_transcribe PRIVATE ${FreeSWITCH_INCLUDE_DIRS})

target_link_libraries(mod_aws_transcribe PRIVATE PkgConfig::FreeSWITCH pthread)
target_link_libraries(mod_aws_transcribe PRIVATE ${AWSSDK_LINK_LIBRARIES})

install(TARGETS mod_aws_transcribe DESTINATION ${FS_MOD_DIR})
