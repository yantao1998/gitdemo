cmake_minimum_required(VERSION 3.21)
project(base CXX C)

set(HEADER_FILES"
    "${GME_DEPS_DIR}/acis/include/mmgr.hxx"
    "${GME_DEPS_DIR}/acis/include/err_info_list.hxx"
    "${GME_DEPS_DIR}/acis/include/mmgr_dbg.hxx"
    "${GME_DEPS_DIR}/acis/include/position.hxx"
    "${GME_DEPS_DIR}/acis/include/errmsg.hxx"
    "${GME_DEPS_DIR}/acis/include/vector.hxx"
    "${GME_DEPS_DIR}/acis/include/unitvec.hxx"
    "gme_alloc.hxx")

set(SOURCE_FILES "base.cpp" "option.cpp" "err_info_base.cpp"
                 "err_info_base_list.cpp" "mmgr.cpp" "mmgr_dbg.cpp" "err_info_list.cpp"
                 "position.cpp" "errmsg.cpp" "gme_alloc.cpp" "vector.cpp" "unitvec.cpp")

add_library(${PROJECT_NAME} SHARED ${HEADER_FILES} ${SOURCE_FILES})
install(TARGETS ${PROJECT_NAME} DESTINATION ${GME_INSTALL_LIB_DIR})

set(BASE_INCLUDES ${GME_INCLUDE_DIR} ${GME_DEPS_DIR})
target_include_directories(${PROJECT_NAME} PUBLIC ${BASE_INCLUDES})

target_link_libraries(
  ${PROJECT_NAME}
  PUBLIC debug "${BASE_DEPS}d.lib"
  PUBLIC optimized "${BASE_DEPS}.lib")

set(OUTPUT_DIR
    "$<$<CONFIG:Debug>:" "${EXECUTABLE_OUTPUT_PATH}/Debug" ">"
    "$<$<NOT:$<CONFIG:Debug>>:" "${EXECUTABLE_OUTPUT_PATH}/Release" ">")
string(REPLACE ";" "" OUTPUT_DIR ${OUTPUT_DIR})
set(INPUT_FILE "$<$<CONFIG:Debug>:" "${BASE_DEPS}d.dll" ">"
               "$<$<NOT:$<CONFIG:Debug>>:" "${BASE_DEPS}.dll" ">")
string(REPLACE ";" "" INPUT_FILE ${INPUT_FILE})
add_custom_command(
  TARGET ${PROJECT_NAME}
  POST_BUILD
  COMMAND ${CMAKE_COMMAND} -E copy ${INPUT_FILE} ${OUTPUT_DIR}/.)
