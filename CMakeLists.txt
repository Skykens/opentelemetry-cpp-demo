
cmake_minimum_required(VERSION 2.20)
project(foo)
set(CMAKE_PREFIX_PATH ${CMAKE_PREFIX_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/3rdparty/opentelemetry-cpp-sdk/")
find_package(opentelemetry-cpp CONFIG REQUIRED)
find_package(CURL)
find_package(Threads REQUIRED)
set(protobuf_MODULE_COMPATIBLE TRUE)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set_target_properties(opentelemetry-cpp::version PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libopentelemetry_version.a")
set_target_properties(opentelemetry-cpp::common PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libopentelemetry_common.a")
set_target_properties(opentelemetry-cpp::trace PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libopentelemetry_trace.a")
set_target_properties(opentelemetry-cpp::metrics PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libopentelemetry_metrics.a")
set_target_properties(opentelemetry-cpp::ostream_span_exporter PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libopentelemetry_exporter_ostream_span.a")
set_target_properties(opentelemetry-cpp::resources PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libopentelemetry_resources.a")
set_target_properties(opentelemetry-cpp::http_client_curl PROPERTIES IMPORTED_LOCATION "${OPENTELEMETRY_CPP_LIBRARY_DIRS}/libhttp_client_curl.a")
find_package(Protobuf CONFIG REQUIRED)
message(STATUS "using protobuf ${Protobuf_VERSION}")

set(_PROTOBUF_LIBPROTOBUF protobuf::libprotobuf)
set(_REFLECTION gRPC::grpc++_reflection)

find_package(gRPC CONFIG REQUIRED)
message(STATUS "using gRPC ${gRPC_VERSION}")

set(_GRPC_GRPCPP gRPC::grpc++)

foreach(_target client server)
    add_executable(${_target} "${_target}.cc"
        ${hw_proto_srcs}
        ${hw_grpc_srcs}
        ${CMAKE_CURRENT_SOURCE_DIR}/protos/messages.grpc.pb.cc
        ${CMAKE_CURRENT_SOURCE_DIR}/protos/messages.grpc.pb.h
        ${CMAKE_CURRENT_SOURCE_DIR}/protos/messages.pb.cc
        ${CMAKE_CURRENT_SOURCE_DIR}/protos/messages.pb.h)
    target_include_directories(${_target} PRIVATE ${OPENTELEMETRY_CPP_INCLUDE_DIRS})
    target_link_libraries(${_target}
        ${_REFLECTION}
        ${_GRPC_GRPCPP}
        ${_PROTOBUF_LIBPROTOBUF}
        ${OPENTELEMETRY_CPP_LIBRARIES})
endforeach()

