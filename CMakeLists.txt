cmake_minimum_required(VERSION 3.2)
project(wasm_dynamic)

add_compile_options(-xxo-rtti)
add_compile_options(-Wall -Wextra -pedantic -Werror -Wfatal-errors)

set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(CMAKE_CXX_STANDARD 20)


find_package(LLVM REQUIRED CONFIG)
message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using LLVMConfig.cmake in: ${LLVM_DIR}")
include_directories(${LLVM_INCLUDE_DIRS})
add_definitions(${LLVM_DEFINITIONS})
llvm_map_components_to_libnames(llvm_libs support core)


add_subdirectory(${PROJECT_SOURCE_DIR}/lib/fmt)
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/mio)
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/preprocessor)
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/utfcpp)
add_subdirectory(${PROJECT_SOURCE_DIR}/lib/range-v3)

add_library(wasm_dynamic
        src/bytecode/Module.cc
        src/bytecode/Validation.cc
        src/mir/Module.cc
        src/mir/Instruction.cc
        src/mir/LLVMCodegen.cc
        src/mir/MIRCodegen.cc
        src/parser/ExprBuilderDelegate.cc
        src/parser/customsections/Name.cc)

target_link_libraries(wasm_dynamic
        fmt::fmt
        mio::mio
        Boost::preprocessor
        utf8::cpp
        range-v3
        ${llvm_libs})

add_executable(sable-wasm src/Main.cc)
target_link_libraries(sable-wasm wasm_dynamic)