cmake_minimum_required(VERSION 3.5)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

# C++17
set(CMAKE_CXX_STANDARD 17)

# generate ASDL files
find_package(ASDL)
ASDL_TARGET(Arithmetic ${CMAKE_CURRENT_SOURCE_DIR}/example/Arithmetic.asdl
    ${CMAKE_CURRENT_BINARY_DIR}/Arithmetic.h
    ${CMAKE_CURRENT_BINARY_DIR}/Arithmetic.cpp)
include_directories(${CMAKE_CURRENT_BINARY_DIR})

add_executable(example example/example.cpp ${ASDL_Arithmetic_OUTPUTS})
