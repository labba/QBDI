set(SOURCES
    QBDITest.cpp
    API/AllocTest.cpp
    API/MemoryAccessTest.cpp
    API/RangeTest.cpp
    API/VMTest.cpp
    ExecBlock/ExecBlockTest.cpp
    ExecBlock/ExecBlockManagerTest.cpp
    TestSetup/LLVMTestEnv.cpp
    Patch/ComparedExecutor_${ARCH}.cpp
    Patch/Instr_${BASE_ARCH}Test.cpp
    Patch/Patch_${BASE_ARCH}Test.cpp
    Miscs/StringTest.cpp
    TestSetup/InMemoryAssembler.cpp
    TestSetup/ShellcodeTester.cpp
)

if(${PLATFORM} STREQUAL "win-X86_64")
    set(SOURCES ${SOURCES}
        Patch/WIN64_RunRealExec.asm
    )
elseif (${PLATFORM} STREQUAL "win-X86")
    set(SOURCES ${SOURCES}
        Patch/WIN86_RunRealExec.asm
    )
    set_source_files_properties("Patch/WIN86_RunRealExec.asm" PROPERTIES COMPILE_FLAGS "/safeseh")
endif()

add_executable(QBDITest ${SOURCES})
add_signature(QBDITest)

target_include_directories(QBDITest PRIVATE
    "${CMAKE_CURRENT_SOURCE_DIR}"
    "${CMAKE_CURRENT_SOURCE_DIR}/../include"
    "${CMAKE_CURRENT_SOURCE_DIR}/../include/QBDI"
    "${CMAKE_CURRENT_SOURCE_DIR}/../src"
    "${CMAKE_CURRENT_SOURCE_DIR}/../deps/llvm/${PLATFORM}/include"
    "${CMAKE_CURRENT_SOURCE_DIR}/../deps/llvm/${PLATFORM}/lib/Target/${LLVM_ARCH}"
    "${CMAKE_CURRENT_SOURCE_DIR}/../deps/gtest/${PLATFORM}/include"
)

find_library(GTEST gtest PATHS
    "${CMAKE_CURRENT_SOURCE_DIR}/../deps/gtest/${PLATFORM}/lib"
    NO_DEFAULT_PATH)


if (${GTEST} STREQUAL "GTEST-NOTFOUND")
    target_link_libraries(QBDITest
        QBDI_static
        "${CMAKE_CURRENT_SOURCE_DIR}/../deps/gtest/${PLATFORM}/lib/libgtest.a"
    )
else()
    target_link_libraries(QBDITest
        QBDI_static
        ${GTEST}
    )
endif()


set_property(TARGET QBDITest PROPERTY CXX_STANDARD 11)
set_property(TARGET QBDITest PROPERTY CXX_STANDARD_REQUIRED ON)
