# SpecLib: Library for speculative execution of loops
# Copyright (C) 2023 Millan A. Martinez, Basilio B. Fraguela, Jose C. Cabaleiro, Francisco F. Rivera. Universidade da Coruna
#
# Distributed under the MIT License. (See accompanying file LICENSE)

# \file     CMakeLists.txt
# \brief    Main CMakeLists.txt file
# \author   Millan A. Martinez  <millan.alvarez@udc.es>
# \author   Basilio B. Fraguela <basilio.fraguela@udc.es>
# \author   Jose C. Cabaleiro   <jc.cabaleiro@usc.es>
# \author   Francisco F. Rivera <ff.rivera@usc.es>

cmake_minimum_required( VERSION 2.8...3.28 )

project( speclib )

if (NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release CACHE STRING
      "Choose the type of build, options are: None Debug Release RelWithDebInfo MinSizeRel."
      FORCE)
endif(NOT CMAKE_BUILD_TYPE)

if(NOT CMAKE_CXX_STANDARD)
  set( CMAKE_CXX_STANDARD 20 CACHE STRING "C++ standard to be used" )
endif(NOT CMAKE_CXX_STANDARD)

add_subdirectory( tests/basic_tests )

install( DIRECTORY ${PROJECT_SOURCE_DIR}/include/speclib
         DESTINATION include
         FILES_MATCHING PATTERN "*.h" PATTERN "*.hpp" )
