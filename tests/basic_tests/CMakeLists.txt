# SpecLib: Library for speculative execution of loops
# Copyright (C) 2023 Millan A. Martinez, Basilio B. Fraguela, Jose C. Cabaleiro, Francisco F. Rivera. Universidade da Coruna
#
# Distributed under the MIT License. (See accompanying file LICENSE)

# \file     CMakeLists.txt
# \brief    CMakeLists.txt for the basic tests of the project
# \author   Millan A. Martinez  <millan.alvarez@udc.es>
# \author   Basilio B. Fraguela <basilio.fraguela@udc.es>
# \author   Jose C. Cabaleiro   <jc.cabaleiro@usc.es>
# \author   Francisco F. Rivera <ff.rivera@usc.es>

cmake_minimum_required( VERSION 2.8...3.28 )

set(tests max_int_test max_vec_test maxmin_vec_test max_noisy_vec_test reduction_test specvec_test despl_vec_test atomicreal_test max_int_test_rev max_vec_test_rev maxmin_vec_test_rev max_noisy_vec_test_rev reduction_test_rev specvec_test_rev despl_vec_test_rev atomicreal_test_rev)

if(NOT SLSHOWSTATS)
  set(SLSHOWSTATS "Disabled" CACHE STRING "Print statistics after execution, options are: Disabled Minimal Detailed" )
endif(NOT SLSHOWSTATS)
set_property(CACHE SLSHOWSTATS PROPERTY STRINGS "Disabled" "Minimal" "Detailed")
if(NOT SLNOCANCEL)
  set(SLNOCANCEL OFF CACHE BOOL "Enable to suppress the cancellation check within loops" )
endif(NOT SLNOCANCEL)
if(NOT SLNOALLOCA)
  set(SLNOALLOCA OFF CACHE BOOL "Enable to suppress the use of 'alloca' in SpecVector and use 'malloc' instead" )
endif(NOT SLNOALLOCA)
if(NOT SLALLOCAINMALLOCH)
  set(SLALLOCAINMALLOCH OFF CACHE BOOL "Enable to indicate that 'alloca' function is defined in <malloc.h> instead of <alloca.h>" )
endif(NOT SLALLOCAINMALLOCH)
if(NOT ENABLE_DELAY)
  set(ENABLE_DELAY OFF CACHE BOOL "Enable artificial delay within loops" )
endif(NOT ENABLE_DELAY)
if(NOT NOLINKTATOMIC)
  set(NOLINKTATOMIC OFF CACHE BOOL "Do not link the atomic library (-latomic)" )
endif(NOT NOLINKTATOMIC)
if(NOT NO16ATOMIC)
  set(NO16ATOMIC OFF CACHE BOOL "Do not use 16-byte atomics" )
endif(NOT NO16ATOMIC)
if(NOT THREADLIB)
  set(THREADLIB "pthread" CACHE STRING "Linking C++ library for <thread> (default: pthread)" )
endif(NOT THREADLIB)

foreach(test ${tests})
  add_executable( ${test} ${test}.cpp )
  if(SLSHOWSTATS)
    if(SLSHOWSTATS STREQUAL "Detailed")
      target_compile_definitions( ${test} PRIVATE SLSTATS )
    elseif(SLSHOWSTATS STREQUAL "Minimal")
      target_compile_definitions( ${test} PRIVATE SLMINIMALSTATS )
    endif(SLSHOWSTATS STREQUAL "Detailed")
  endif(SLSHOWSTATS)
  if(SLNOCANCEL)
    target_compile_definitions( ${test} PRIVATE SLNOCANCEL )
  endif(SLNOCANCEL)
  if(SLNOALLOCA)
    target_compile_definitions( ${test} PRIVATE NOALLOCA )
  endif(SLNOALLOCA)
  if(SLALLOCAINMALLOCH)
    target_compile_definitions( ${test} PRIVATE ALLOCAINMALLOCH )
  endif(SLALLOCAINMALLOCH)
  if(ENABLE_DELAY)
    target_compile_definitions( ${test} PRIVATE ENABLE_DELAY )
  endif(ENABLE_DELAY)
  target_include_directories( ${test} PRIVATE ${PROJECT_SOURCE_DIR}/include )
  if(THREADLIB)
    target_link_libraries( ${test} ${THREADLIB} )
  endif(THREADLIB)
endforeach(test)
if(NO16ATOMIC)
  target_compile_definitions( atomicreal_test PRIVATE NOLONGDOUBLE )
  target_compile_definitions( atomicreal_test_rev PRIVATE NOLONGDOUBLE )
endif(NO16ATOMIC)
if(NOT NOLINKTATOMIC)
  target_link_libraries( atomicreal_test atomic )
  target_link_libraries( atomicreal_test_rev atomic )
endif(NOT NOLINKTATOMIC)

add_custom_target(check)
foreach(test ${tests})
  add_custom_command(TARGET check PRE_BUILD
                     COMMAND ${CMAKE_COMMAND} -E echo test ${test} :
                     COMMAND ${test} -t 4 -n 100000 -c 20 -N 10 || exit 1
				     COMMAND ${CMAKE_COMMAND} -E echo ------------------------ ) 
endforeach(test)
add_custom_command(TARGET check PRE_BUILD COMMAND echo TESTS DONE. SUCCESSFUL)

install( TARGETS ${tests}
		 RUNTIME DESTINATION ${PROJECT_SOURCE_DIR}/bin )
