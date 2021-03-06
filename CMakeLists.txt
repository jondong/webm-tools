##  Copyright (c) 2015 The WebM project authors. All Rights Reserved.
##
##  Use of this source code is governed by a BSD-style license
##  that can be found in the LICENSE file in the root of the source
##  tree. An additional intellectual property rights grant can be found
##  in the file PATENTS.  All contributing project authors may
##  be found in the AUTHORS file in the root of the source tree.
cmake_minimum_required(VERSION 2.8)
project(webm-tools)

include(ExternalProject)

set(WEBMTOOLS_SRC_DIR "${CMAKE_CURRENT_SOURCE_DIR}")

ExternalProject_Add(libwebm
  PREFIX libwebm
  GIT_REPOSITORY https://github.com/webmproject/libwebm.git
  GIT_TAG master
  PATCH_COMMAND git apply ${CMAKE_CURRENT_SOURCE_DIR}/libwebm.patch
  INSTALL_COMMAND ""
)

ExternalProject_Get_Property(libwebm source_dir binary_dir)
include_directories(${source_dir})

add_library(${PROJECT_NAME} SHARED
  "${WEBMTOOLS_SRC_DIR}/shared/indent.cc"
  "${WEBMTOOLS_SRC_DIR}/shared/indent.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_chunk_writer.cc"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_chunk_writer.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_endian.cc"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_endian.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_file.cc"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_file.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_incremental_reader.cc"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_incremental_reader.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_live_muxer.cc"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_live_muxer.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_constants.h"
  "${WEBMTOOLS_SRC_DIR}/shared/webm_tools_types.h")

add_dependencies(${PROJECT_NAME} libwebm)

target_link_libraries(${PROJECT_NAME} LINK_PUBLIC
  ${binary_dir}/libmkvmuxer.so
  ${binary_dir}/libmkvparser.so
  ${binary_dir}/libwebm_parser.so
  ${binary_dir}/libwebm.so)

install(TARGETS ${PROJECT_NAME} DESTINATION lib)
