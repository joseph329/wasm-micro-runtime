# Copyright (C) 2019 Intel Corporation.  All rights reserved.
# SPDX-License-Identifier: Apache-2.0 WITH LLVM-exception
#

cmake_minimum_required (VERSION 2.8.3)
project (host-agent)

if (NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Debug)
endif (NOT CMAKE_BUILD_TYPE)

if (NOT WAMR_BUILD_PLATFORM)
  set (WAMR_BUILD_PLATFORM "linux")
endif (NOT WAMR_BUILD_PLATFORM)

message ("WAMR_BUILD_PLATFORM = " ${WAMR_BUILD_PLATFORM})

add_definitions(-DWA_MALLOC=malloc)
add_definitions(-DWA_FREE=free)

set (REPO_ROOT_DIR ${CMAKE_CURRENT_LIST_DIR}/../..)
set (IWASM_DIR  ${REPO_ROOT_DIR}/core/iwasm)
set (APP_MGR_DIR ${REPO_ROOT_DIR}/core/app-mgr)
set (SHARED_DIR ${REPO_ROOT_DIR}/core/shared)
set (APP_FRAMEWORK_DIR ${REPO_ROOT_DIR}/core/app-framework)
#TODO: use soft-plc/tools/iec-runtime/external/cJSON instead
set (CJSON_DIR ${CMAKE_CURRENT_LIST_DIR}/external/cJSON)

include (${APP_FRAMEWORK_DIR}/app-native-shared/native_interface.cmake)
include (${APP_MGR_DIR}/app-mgr-shared/app_mgr_shared.cmake)
include (${SHARED_DIR}/platform/${WAMR_BUILD_PLATFORM}/shared_platform.cmake)
include (${SHARED_DIR}/utils/shared_utils.cmake)
include (${SHARED_DIR}/mem-alloc/mem_alloc.cmake)
include (${CJSON_DIR}/cjson.cmake)
include (${SHARED_DIR}/coap/lib_coap.cmake)

if (CMAKE_SIZEOF_VOID_P EQUAL 8)
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -m32")
  SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -m32")
endif ()

add_definitions(-Wall -Wno-pointer-sign -DMALLOC_MEMORY_FROM_SYSTEM)

include_directories(
    ${CMAKE_CURRENT_LIST_DIR}/src
    ${IWASM_DIR}/include
)

file (GLOB_RECURSE HOST_TOOL_SRC src/*.c)


SET(SOURCES
    ${HOST_TOOL_SRC}
    ${PLATFORM_SHARED_SOURCE}
    ${UTILS_SHARED_SOURCE}
    ${NATIVE_INTERFACE_SOURCE}
    ${CJSON_SOURCE}
    ${LIB_HOST_AGENT_SOURCE}
    )
    
add_executable(host_tool ${SOURCES})
target_link_libraries(host_tool pthread)
