##*****************************************************************//
##    Albany 3.0:  Copyright 2016 Sandia Corporation               //
##    This Software is released under the BSD license detailed     //
##    in the file "license.txt" in the top-level Albany directory  //
##*****************************************************************//

if (ALBANY_IFPACK2 AND SEACAS_EXODIFF)
# Create a symlink to exodiff
execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink
  ${SEACAS_EXODIFF} ${CMAKE_CURRENT_BINARY_DIR}/exodiff)
execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink
  ${AlbanyTPath} ${CMAKE_CURRENT_BINARY_DIR}/AlbanyT)

# input files
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/input.yaml
               ${CMAKE_CURRENT_BINARY_DIR}/input.yaml COPYONLY)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/surface1ElemMesh.g
               ${CMAKE_CURRENT_BINARY_DIR}/surface1ElemMesh.g COPYONLY)

# material files
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/1material.yaml
               ${CMAKE_CURRENT_BINARY_DIR}/1material.yaml COPYONLY)

# exodiff files
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/out.exodiff
               ${CMAKE_CURRENT_BINARY_DIR}/out.exodiff COPYONLY)

# gold files
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/out.gold.e
               ${CMAKE_CURRENT_BINARY_DIR}/out.gold.e COPYONLY)

# python runtest file
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/runtest.cmake
               ${CMAKE_CURRENT_BINARY_DIR}/runtest.cmake COPYONLY)

# 2. Name the test with the directory name
get_filename_component(testName ${CMAKE_CURRENT_SOURCE_DIR} NAME)

# 3. Create the test with this name and standard executable
IF(NOT ALBANY_PARALLEL_ONLY)
  SET(OUTFILE  "out.e")
  SET(REF_FILE "out.gold.e")
   add_test(NAME ${testName}
           COMMAND ${CMAKE_COMMAND} "-DTEST_PROG=${SerialAlbanyT.exe}"
           -DTEST_NAME=out -DTEST_ARGS=input.yaml -DMPIMNP=1
           -DSEACAS_EXODIFF=${SEACAS_EXODIFF}
           -DREF_FILENAME=${REF_FILE} -DOUTPUT_FILENAME=${OUTFILE}
           -DDATA_DIR=${CMAKE_CURRENT_SOURCE_DIR} -P ${CMAKE_CURRENT_SOURCE_DIR}/runtest.cmake)

ENDIF()
endif()
