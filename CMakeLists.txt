CMAKE_MINIMUM_REQUIRED(VERSION 3.0)
option(BUILD_SHARED_LIBS "Build using shared libraries" OFF)
PROJECT(Quartet)

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -O3 -DNDEBUG -Wall -funroll-loops")

SET(SRC_FILES src/predicates.cpp
              src/geometry_queries.cpp
              src/sdf.cpp
              src/trimesh.cpp
              src/tet_mesh.cpp
              src/feature.cpp
              src/read_obj.cpp
              src/tet_quality.cpp
              src/match_features.cpp
              src/optimize_tet_mesh.cpp
              src/make_signed_distance.cpp
              src/make_tet_mesh.cpp)

ADD_LIBRARY(quartet_obj OBJECT ${SRC_FILES})
ADD_LIBRARY(quartet $<TARGET_OBJECTS:quartet_obj>)
if(UNIX)
    TARGET_COMPILE_OPTIONS(quartet PRIVATE -fPIC)
    TARGET_LINK_LIBRARIES(quartet PRIVATE m)
endif(UNIX)

ADD_EXECUTABLE(quartet_bin src/main.cpp)
if(WIN32 AND BUILD_SHARED_LIBS)
    ADD_LIBRARY(quartet_static STATIC  $<TARGET_OBJECTS:quartet_obj>)
    TARGET_LINK_LIBRARIES(quartet_bin PRIVATE quartet_static)
else()
    TARGET_LINK_LIBRARIES(quartet_bin PRIVATE quartet)
endif()

SET_TARGET_PROPERTIES(quartet_bin PROPERTIES OUTPUT_NAME quartet)

INSTALL(TARGETS quartet quartet_bin
    LIBRARY DESTINATION lib
    ARCHIVE DESTINATION lib
    RUNTIME DESTINATION bin
    )
INSTALL(FILES
    src/array3.h
    src/feature.h
    src/geometry_queries.h
    src/gluvi.h
    src/make_signed_distance.h
    src/make_tet_mesh.h
    src/match_features.h
    src/optimize_tet_mesh.h
    src/read_obj.h
    src/sdf.h
    src/tet_mesh.h
    src/tet_quality.h
    src/trimesh.h
    src/util.h
    src/vec.h
    DESTINATION include/quartet)
