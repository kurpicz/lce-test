################################################################################
# CMakeLists.txt
#
# Copyright (C) 2019 Alexander Herlez <alexander.herlez@tu-dortmund.de>
# Copyright (C) 2019 Florian Kurpicz <florian.kurpicz@tu-dortmund.de>
#
# All rights reserved. Published under the BSD-2 license in the LICENSE file.
################################################################################

# include malloc_count
add_library(malloc_count
  ${PROJECT_SOURCE_DIR}/extlib/malloc_count/malloc_count.c
  ${PROJECT_SOURCE_DIR}/extlib/malloc_count/stack_count.c
)
  
target_include_directories(malloc_count PUBLIC
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/extlib/malloc_count/>
  $<INSTALL_INTERFACE:${PROJECT_SOURCE_DIR}/extlib/malloc_count/>
)

add_executable(bench_time
  bench_time.cpp)

find_package(SDSL REQUIRED)
find_package(divsufsort REQUIRED)
find_package(divsufsort64 REQUIRED)

target_include_directories(bench_time PUBLIC
  $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/lce-test/>
  $<INSTALL_INTERFACE:${PROJECT_SOURCE_DIR}/lce-test/>
  ${PROJECT_SOURCE_DIR}/extlib/stash/include
  ${SDSL_INCLUDE_DIRS}
)

target_link_libraries(bench_time
  ferrada_rmq tlx stdc++fs sais_lcp ${SDSL} ${divsufsort} ${divsufsort64} malloc_count -ldl)

option(LCE_BUILD_SDSL
  "Build the minimal benchmark based on the SDSL which uses CSTs to compute LCEs" OFF)

if (LCE_BUILD_SDSL)

  find_package(SDSL REQUIRED)
  find_package(divsufsort REQUIRED)
  find_package(divsufsort64 REQUIRED)

  add_executable(sdsl_cst_benchmark
    sdsl_cst_benchmark.cpp)
  target_link_libraries(sdsl_cst_benchmark ${SDSL} ${divsufsort} ${divsufsort64})

  target_include_directories(sdsl_cst_benchmark PUBLIC
    ${SDSL_INCLUDE_DIRS}
    $<BUILD_INTERFACE:${CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CURRENT_SOURCE_DIR}>
    )

endif()

# add_executable(bench_memory ./src/bench_memory.cpp)
# target_link_libraries(bench_memory ferrada_rmq tlx_command_line sais_lcp)
# target_link_libraries(bench_memory malloc_count -ldl)

################################################################################
