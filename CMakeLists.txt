cmake_minimum_required(VERSION 3.0)
project(tinyrpc)


set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

SET(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR})
enable_language(ASM)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0 -std=c++11 -Wall -Wno-deprecated -Wno-unused-but-set-variable")

set(PATH_LIB lib)
set(PATH_BIN bin)
set(PATH_TESTCASES testcases)

include_directories(${PROJECT_SOURCE_DIR})
# *.cc
aux_source_directory(${PROJECT_SOURCE_DIR}/tinyrpc/comm COMM)
aux_source_directory(${PROJECT_SOURCE_DIR}/tinyrpc/coroutine COR)
aux_source_directory(${PROJECT_SOURCE_DIR}/tinyrpc/net NET)
aux_source_directory(${PROJECT_SOURCE_DIR}/tinyrpc/net/http HTTP)
aux_source_directory(${PROJECT_SOURCE_DIR}/tinyrpc/net/tcp TCP)
aux_source_directory(${PROJECT_SOURCE_DIR}/tinyrpc/net/tinypb TINYPB)

set(COXTX ${PROJECT_SOURCE_DIR}/tinyrpc/coroutine/coctx_swap.S)

add_library(tinyrpc ${COMM} ${COR} ${NET} ${HTTP} ${TCP} ${TINYPB} ${COXTX})
install(TARGETS tinyrpc DESTINATION ${PATH_LIB})


find_package(Protobuf REQUIRED)
if (${PROTOBUF_FOUND})
    message("protobuf found")
else ()
    message(FATAL_ERROR "Cannot find Protobuf")
endif ()

set(tinyxml /usr/lib/libtinyxml.a)
set(Protobuf_lib /usr/local/lib/libprotobuf.so)

set(LIBS
    tinyrpc
    ${Protobuf_LIBRARY}
    ${tinyxml}
    ${Protobuf_lib}
    dl
    pthread
)
message(${Protobuf_LIBRARY})


# test_tinypb_server
# set(
#     test_tinypb_server
#     ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_tinypb_server.cc
#     ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_tinypb_server.pb.cc
# )
# add_executable(test_tinypb_server ${test_tinypb_server})
# target_link_libraries(test_tinypb_server protobuf)
# target_link_libraries(test_tinypb_server ${LIBS})
# install(TARGETS test_tinypb_server DESTINATION ${PATH_BIN})

# test_tinypb_server_client
# set(
#     test_tinypb_server_client
#     ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_tinypb_server_client.cc
#     ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_tinypb_server.pb.cc
# )
# add_executable(test_tinypb_server_client ${test_tinypb_server_client})
# target_link_libraries(test_tinypb_server_client protobuf)
# target_link_libraries(test_tinypb_server_client ${LIBS})
# install(TARGETS test_tinypb_server_client DESTINATION ${PATH_BIN})


# set(
#     test_http_server
#     ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_http_server.cc
#     ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_tinypb_server.pb.cc
# )
# add_executable(test_http_server ${test_http_server})
# target_link_libraries(test_http_server protobuf)
# target_link_libraries(test_http_server ${LIBS})
# target_link_libraries(test_http_server protobuf)
# install(TARGETS test_http_server DESTINATION ${PATH_BIN})

# test_coroutine
set(
    test_coroutine
    ${PROJECT_SOURCE_DIR}/${PATH_TESTCASES}/test_coroutine.cc
)
add_executable(test_coroutine ${test_coroutine})
target_link_libraries(test_coroutine ${LIBS})
target_link_libraries(test_coroutine protobuf)
install(TARGETS test_coroutine DESTINATION ${PATH_BIN})

# install *.h
add_subdirectory(tinyrpc/comm)
add_subdirectory(tinyrpc/coroutine)
add_subdirectory(tinyrpc/net)
add_subdirectory(tinyrpc/net/http)
add_subdirectory(tinyrpc/net/tcp)
add_subdirectory(tinyrpc/net/tinypb)