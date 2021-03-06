# Compiler properties

set(CMAKE_C_FLAGS ORG_CMAKE_C_FLAGS)
string(CONCAT CMAKE_C_FLAGS ${DFT_C_FLAGS})

set(COMMON_TARGET_PROPERTIES 
  C_STANDARD 99                  # -std=gnu99
  )

#

function(add_test_dft TESTNAME)
  if (NOT EMULATOR AND NOT SDE_COMMAND)
    add_test(NAME ${TESTNAME} COMMAND ${ARGN})
  elseif(NOT EMULATOR)
    add_test(NAME ${TESTNAME} COMMAND ${SDE_COMMAND} "--" ${ARGN})
  else()
    add_test(NAME ${TESTNAME} COMMAND ${EMULATOR} ${ARGN})
  endif()
endfunction()

# Include directories

include_directories(${PROJECT_SOURCE_DIR}/include)        # sleefdft.h
include_directories(${CMAKE_BINARY_DIR}/include)          # sleef.h

# Link directories

link_directories(${CMAKE_BINARY_DIR}/lib)                 # libsleef, libsleefdft

# Link libraries

set(COMMON_LINK_LIBRARIES ${TARGET_LIBSLEEF} ${TARGET_LIBDFT})

if (COMPILER_SUPPORTS_OPENMP)
  set(COMMON_LINK_LIBRARIES ${COMMON_LINK_LIBRARIES} ${OpenMP_C_FLAGS})
endif()

if(MSVC)
  message(STATUS "The testing program for DFT is currently not available with MSVC build - skip building tests dft-tester")
else()
  # Target executable naivetestdp

  set(TARGET_NAIVETESTDP "naivetestdp")
  add_executable(${TARGET_NAIVETESTDP} naivetest.c ${PROJECT_SOURCE_DIR}/include/sleefdft.h)
  add_dependencies(${TARGET_NAIVETESTDP} ${TARGET_HEADERS} ${TARGET_LIBSLEEF} ${TARGET_LIBDFT})
  target_compile_definitions(${TARGET_NAIVETESTDP} PRIVATE ${COMMON_TARGET_DEFINITIONS} BASETYPEID=1)
  target_link_libraries(${TARGET_NAIVETESTDP} ${COMMON_LINK_LIBRARIES})
  set_target_properties(${TARGET_NAIVETESTDP} PROPERTIES ${COMMON_TARGET_PROPERTIES})

  # Target executable naivetestsp

  set(TARGET_NAIVETESTSP "naivetestsp")
  add_executable(${TARGET_NAIVETESTSP} naivetest.c ${PROJECT_SOURCE_DIR}/include/sleefdft.h)
  add_dependencies(${TARGET_NAIVETESTSP} ${TARGET_HEADERS} ${TARGET_LIBSLEEF} ${TARGET_LIBDFT})
  target_compile_definitions(${TARGET_NAIVETESTSP} PRIVATE ${COMMON_TARGET_DEFINITIONS} BASETYPEID=2)
  target_link_libraries(${TARGET_NAIVETESTSP} ${COMMON_LINK_LIBRARIES})
  set_target_properties(${TARGET_NAIVETESTSP} PROPERTIES ${COMMON_TARGET_PROPERTIES})

  # Test naivetestdp
  add_test_dft(${TARGET_NAIVETESTDP}_1 $<TARGET_FILE:${TARGET_NAIVETESTDP}> 1)
  add_test_dft(${TARGET_NAIVETESTDP}_2 $<TARGET_FILE:${TARGET_NAIVETESTDP}> 2)
  add_test_dft(${TARGET_NAIVETESTDP}_3 $<TARGET_FILE:${TARGET_NAIVETESTDP}> 3)
  add_test_dft(${TARGET_NAIVETESTDP}_4 $<TARGET_FILE:${TARGET_NAIVETESTDP}> 4)
  add_test_dft(${TARGET_NAIVETESTDP}_5 $<TARGET_FILE:${TARGET_NAIVETESTDP}> 5)
  add_test_dft(${TARGET_NAIVETESTDP}_10 $<TARGET_FILE:${TARGET_NAIVETESTDP}> 10)

  # Test naivetestsp
  add_test_dft(${TARGET_NAIVETESTSP}_1 $<TARGET_FILE:${TARGET_NAIVETESTSP}> 1)
  add_test_dft(${TARGET_NAIVETESTSP}_2 $<TARGET_FILE:${TARGET_NAIVETESTSP}> 2)
  add_test_dft(${TARGET_NAIVETESTSP}_3 $<TARGET_FILE:${TARGET_NAIVETESTSP}> 3)
  add_test_dft(${TARGET_NAIVETESTSP}_4 $<TARGET_FILE:${TARGET_NAIVETESTSP}> 4)
  add_test_dft(${TARGET_NAIVETESTSP}_5 $<TARGET_FILE:${TARGET_NAIVETESTSP}> 5)
  add_test_dft(${TARGET_NAIVETESTSP}_10 $<TARGET_FILE:${TARGET_NAIVETESTSP}> 10)
endif(MSVC)
