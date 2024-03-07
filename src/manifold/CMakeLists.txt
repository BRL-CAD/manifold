# Copyright 2020 The Manifold Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

project (manifold)

file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS *.cpp)
set(SOURCE_FILES ${SOURCE_FILES}
  	${PROJECT_SOURCE_DIR}/../third_party/quickhull/QuickHull.cpp)

if(MANIFOLD_EXPORT)
	find_package(ASSETIMPORT REQUIRED)
	set(SOURCE_FILES ${SOURCE_FILES}
		${PROJECT_SOURCE_DIR}/../../meshIO/src/meshIO.cpp)
	include_directories(
		${PROJECT_SOURCE_DIR}/../../meshIO/include
		${ASSETIMPORT_INCLUDE_DIR}
		)
	install(FILES ${PROJECT_SOURCE_DIR}/../../meshIO/include/meshIO.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/manifold)
endif(MANIFOLD_EXPORT)

add_library(${PROJECT_NAME} ${SOURCE_FILES})

target_include_directories(${PROJECT_NAME} PUBLIC
    $<INSTALL_INTERFACE:include/${CMAKE_PROJECT_NAME}>
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../third_party/quickhull>
    $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/../../meshIO/include>
	 )
target_link_libraries(${PROJECT_NAME}
    PUBLIC utilities cross_section sdf
	 PRIVATE collider polygon ${MANIFOLD_INCLUDE} ${ASSETIMPORT_LIBRARY}
)

target_compile_options(${PROJECT_NAME} PRIVATE ${MANIFOLD_FLAGS})

target_compile_features(${PROJECT_NAME}
    PUBLIC cxx_std_17
)

install(TARGETS ${PROJECT_NAME} EXPORT manifoldTargets)
install(FILES include/manifold.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${CMAKE_PROJECT_NAME})
