# SpecLib: Library for speculative execution of loops
# Copyright (C) 2023 Millan A. Martinez, Basilio B. Fraguela, Jose C. Cabaleiro, Francisco F. Rivera. Universidade da Coruna
#
# Distributed under the MIT License. (See accompanying file LICENSE)

# \file     CMakeLists.txt
# \brief    CMakeLists.txt for the tests of the project
# \author   Millan A. Martinez  <millan.alvarez@udc.es>
# \author   Basilio B. Fraguela <basilio.fraguela@udc.es>
# \author   Jose C. Cabaleiro   <jc.cabaleiro@usc.es>
# \author   Francisco F. Rivera <ff.rivera@usc.es>

set(tests max_int_test max_vec_test maxmin_vec_test max_noisy_vec_test reduction_test specvec_test max_int_test_rev max_vec_test_rev maxmin_vec_test_rev max_noisy_vec_test_rev reduction_test_rev specvec_test_rev)

foreach(test ${tests})
  add_executable( ${test} ${test}.cpp )
  target_include_directories( ${test} PRIVATE ${PROJECT_SOURCE_DIR}/include )
  target_link_libraries( ${test} pthread )
endforeach(test)

add_custom_target(check)
foreach(test ${tests})
  add_custom_command(TARGET check PRE_BUILD
                     COMMAND ${CMAKE_COMMAND} -E echo test ${test} :
                     COMMAND ${test} -t 4 -n 100000 -c 20 -N 10 || exit 1
				     COMMAND ${CMAKE_COMMAND} -E echo ------------------------ ) 
endforeach(test)
add_custom_command(TARGET check PRE_BUILD COMMAND echo TESTS DONE. SUCCESSFUL)
