cmake_minimum_required(VERSION 2.8.3)
project(suitesparse_catkin)

set(CMAKE_BUILD_TYPE Release)

find_package(catkin_simple REQUIRED)

catkin_simple(ALL_DEPS_REQUIRED)

include(ExternalProject)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include)
file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/lib)

SET(SUITESPARSE_SRC_PATH "suitesparse_src")

ExternalProject_Add(suitesparse_src
  URL https://github.com/jluttine/suitesparse/archive/v4.4.2.zip
  URL_MD5 08b542b41918ff25a692d578a804be12
  PATCH_COMMAND sed -i.bu "s@/usr/local/lib@${CATKIN_DEVEL_PREFIX}/lib@g" SuiteSparse_config/SuiteSparse_config.mk && sed -i.bu "s@/usr/local/include@${CATKIN_DEVEL_PREFIX}/include@g" SuiteSparse_config/SuiteSparse_config.mk
  UPDATE_COMMAND ""
  SOURCE_DIR ${SUITESPARSE_SRC_PATH}
  BINARY_DIR suitesparse_build
  CONFIGURE_COMMAND ""
  BUILD_COMMAND export CFLAGS=-DNTIMER && cd ../${SUITESPARSE_SRC_PATH} && make library -l8 -j8
  INSTALL_COMMAND cd ../${SUITESPARSE_SRC_PATH} && make install -j8
)
cs_add_library(${PROJECT_NAME} src/dependency_tracker.cc)
add_dependencies(${PROJECT_NAME} suitesparse_src)
target_link_libraries(${PROJECT_NAME}
  ${CATKIN_DEVEL_PREFIX}/lib/libamd.2.4.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcamd.2.4.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcholmod.3.0.3.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcxsparse.3.1.4.a
  ${CATKIN_DEVEL_PREFIX}/lib/libldl.2.2.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libspqr.2.0.0.a
  ${CATKIN_DEVEL_PREFIX}/lib/libumfpack.5.7.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libamd.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcamd.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcholmod.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcxsparse.a
  ${CATKIN_DEVEL_PREFIX}/lib/libldl.a
  ${CATKIN_DEVEL_PREFIX}/lib/libspqr.a
  ${CATKIN_DEVEL_PREFIX}/lib/libumfpack.a
  ${CATKIN_DEVEL_PREFIX}/lib/libbtf.1.2.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libccolamd.2.9.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcolamd.2.9.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libklu.1.3.2.a
  ${CATKIN_DEVEL_PREFIX}/lib/librbio.2.2.1.a
  ${CATKIN_DEVEL_PREFIX}/lib/libsuitesparseconfig.4.4.0.a
  ${CATKIN_DEVEL_PREFIX}/lib/libbtf.a
  ${CATKIN_DEVEL_PREFIX}/lib/libccolamd.a
  ${CATKIN_DEVEL_PREFIX}/lib/libcolamd.a
  ${CATKIN_DEVEL_PREFIX}/lib/libklu.a
  ${CATKIN_DEVEL_PREFIX}/lib/librbio.a
  ${CATKIN_DEVEL_PREFIX}/lib/libsuitesparseconfig.a)

cs_install()

cs_export(INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include
 CFG_EXTRAS suitesparse-extras.cmake)
