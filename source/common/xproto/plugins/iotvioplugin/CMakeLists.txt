cmake_minimum_required(VERSION 2.8)

option(IOT_VIO_API "iot vio api" OFF)
# 项目名称
project(vioplugin)

# 添加头文件路径
include_directories(
    ${PROJECT_SOURCE_DIR}/include
    ${PROJECT_SOURCE_DIR}/third_party/include
)

# 头文件路径信息
file(GLOB PROJECT_INC
    "include/vioplugin/*.h"
    "include/iotviomanager/*.h"
    "include/utils/*.h"
)

# 源文件路径信息
if (${IOT_VIO_API})
    add_definitions(-DSUBSYS_NAME=IOT_VIO)
    file(GLOB PROJECT_SRC
        "src/vioplugin/*.cpp"
        "src/iotviomanager/*.cpp"
        "src/utils/*.cpp")
else ()
    file(GLOB PROJECT_SRC
        "src/vioplugin/*.cpp"
        "src/utils/*.cpp")
endif ()

set(SOURCE_FILES ${PROJECT_SRC} ${PROJECT_INC})
add_library(${PROJECT_NAME} SHARED ${SOURCE_FILES})

set(BASE_LIBRARIES opencv_world pthread vio cam uvc xproto_msgtype jsoncpp xstream-imagetools)

set(X3_HAPI_LIB isp iar isp_algo tinyalsa multimedia z avformat hbmedia)
target_link_libraries(${PROJECT_NAME} ${BASE_LIBRARIES} ${X3_HAPI_LIB})

#add_subdirectory(test)

set(MY_OUTPUT_ROOT ${OUTPUT_ROOT}/${PROJECT_NAME}/)
install(DIRECTORY ${PROJECT_SOURCE_DIR}/include/
    DESTINATION ${MY_OUTPUT_ROOT}/include/${PROJECT_NAME})
install(TARGETS ${PROJECT_NAME} DESTINATION ${MY_OUTPUT_ROOT}/lib)
# install configs, some scripts will depends this
install(DIRECTORY
    ${CMAKE_CURRENT_SOURCE_DIR}/configs/
    DESTINATION ${MY_OUTPUT_ROOT}/config/)

