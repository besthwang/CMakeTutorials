cmake_minimum_required(VERSION 3.10)
project(MyLib)


set (MYLIB_VERSION_NUM "1.0.0")
set (MYLIB_WORLD_VERSION 1)
set (MYLIB_MAJOR_VERSION 0)
set (MYLIB_MINOR_VERSION 0)

set ( MYLIB_VERSION_STRING ${MYLIB_VERSION_NUMBER} )
set ( MYLIB_VERSION_MAJOR  ${MYLIB_WORLD_VERSION} )
set ( MYLIB_VERSION_MINOR  ${MYLIB_MAJOR_VERSION} )
set ( MYLIB_VERSION_PATCH  ${MYLIB_MINOR_VERSION} )
set ( MYLIB_DEFINITIONS "")
set ( MYLIB_INCLUDE_DIR "${CMAKE_INSTALL_PREFIX}/${INCLUDE_INSTALL_DIR}" )
set ( MYLIB_ROOT_DIR ${CMAKE_INSTALL_PREFIX} )

message(STATUS "MYLIB_INCLUDE_DIR: ${CMAKE_INSTALL_PREFIX}/${INCLUDE_INSTALL_DIR}---${CMAKE_CURRENT_LIST_DIR}")

# 정적 라이브러리 생성
add_library(MyLib STATIC src/mylib.cpp)

# 헤더 파일 경로 설정 (빌드 중에 사용)
target_include_directories(MyLib
    INTERFACE 
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>   # 빌드 시 참조 경로
        $<INSTALL_INTERFACE:include>                             # 설치 후 참조 경로
)

include(GNUInstallDirs)
include (CMakePackageConfigHelpers)

message(STATUS "CMAKE_INSTALL_DATADIR ${CMAKE_INSTALL_DATADIR}")

# MyLib을 lib 디렉토리에 설치하고, 헤더 파일을 include 디렉토리에 설치
install(TARGETS MyLib
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION include)


set(CMAKEPACKAGE_INSTALL_DIR
"${CMAKE_INSTALL_DATADIR}/MyLib/cmake"
CACHE PATH "The directory relative to CMAKE_PREFIX_PATH where MyLibConfig.cmake is installed"
)        

# Export as title case MyLib
set_target_properties(MyLib PROPERTIES EXPORT_NAME MyLib)

install (TARGETS MyLib EXPORT MyLibTargets)

configure_package_config_file (
        ${CMAKE_CURRENT_SOURCE_DIR}/cmake/MyLibConfig.cmake.in
        ${CMAKE_CURRENT_BINARY_DIR}/MyLibConfig.cmake
        PATH_VARS MYLIB_INCLUDE_DIR MYLIB_ROOT_DIR
        INSTALL_DESTINATION ${CMAKEPACKAGE_INSTALL_DIR}
        NO_CHECK_REQUIRED_COMPONENTS_MACRO # Eigen does not provide components
      )

install ( FILES ${CMAKE_CURRENT_SOURCE_DIR}/cmake/UseMyLib.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/MyLibConfig.cmake
DESTINATION ${CMAKEPACKAGE_INSTALL_DIR} )    

export (TARGETS MyLib NAMESPACE MyLib:: FILE MyLibTargets.cmake)
export (PACKAGE MyLib)
install (EXPORT MyLibTargets NAMESPACE MyLib:: DESTINATION ${CMAKEPACKAGE_INSTALL_DIR})

# # 헤더 파일 설치
install(FILES include/mylib.h DESTINATION include)