# MyApp CMakeLists.txt

cmake_minimum_required(VERSION 3.14)
project(MyApp)

# 패키지 찾기
find_package(MyLib CONFIG REQUIRED)

# 실행 파일 타겟 생성
add_executable(MyApp main.cpp)

# MyLib 패키지의 타겟을 링크
target_link_libraries(MyApp PRIVATE MyLib::MyLib)