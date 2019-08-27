cmake_minimum_required(VERSION 3.1)
project(json2pb)

# depend jansson 2.12
execute_process(COMMAND conan install ${CMAKE_CURRENT_SOURCE_DIR}/conanfile.txt --build missing)
include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

set(IncludeLists
    /usr/include/
    /usr/local/include/
    ./
)  
set(LinkLists
    /usr/lib
    /usr/local/lib
    ./
)

include_directories(${IncludeLists})
link_directories(${LinkLists})

set(SOURCES
    ${PROJECT_SOURCE_DIR}/json2pb.h
    ${PROJECT_SOURCE_DIR}/json2pb.cc
)

OPTION(TEST "use test cpp" OFF)
if (TEST)
    list(APPEND SOURCES 
        ${PROJECT_SOURCE_DIR}/test_json.cc
        ${PROJECT_SOURCE_DIR}/test.pb.h
        ${PROJECT_SOURCE_DIR}/test.pb.cc
    )
    message(STATUS "${SOURCES}")
    add_executable(${PROJECT_NAME} ${SOURCES})
else()
    message(STATUS "${SOURCES}")
    add_library (${PROJECT_NAME} ${SOURCES})
endif()

set(LINK_LIBS
    protobuf
    ${CONAN_LIBS}
)

target_link_libraries(${PROJECT_NAME} ${LINK_LIBS})

set(prefix      ${CMAKE_INSTALL_PREFIX})
set(exec_prefix "\${prefix}")
set(libdir      "\${exec_prefix}/${JANSSON_INSTALL_LIB_DIR}")
set(includedir  "\${prefix}/${JANSSON_INSTALL_INCLUDE_DIR}")

set(JSON2PB_HDR_PUBLIC
    ${PROJECT_SOURCE_DIR}/json2pb.h
)

option(JSON2PB_INSTALL "Generate installation target" ON)
if (JSON2PB_INSTALL)
    install(TARGETS json2pb
            EXPORT json2pbTargets
            LIBRARY DESTINATION "lib"
            ARCHIVE DESTINATION "lib"
            RUNTIME DESTINATION "bin"
            INCLUDES DESTINATION "include")

    install(FILES ${JSON2PB_HDR_PUBLIC}
            DESTINATION "include")
endif()

