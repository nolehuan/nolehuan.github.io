---
title: cmake
date: 2023-12-02 16:30:29
abstracts: cmake
tags:
  - cpp
  - cmake
categories:
  - coding
cover: ''
feature: false
mathjax: false
---

#### cmake

命令
`add_compile_options` 针对所有编译器添加编译选项，CMAKE_C_FLAGS 与 CMAKE_CXX_FLAGS 分别针对 c 和 c++ 编译器
`aux_source_directory(dir var)` 将目录中的源文件保存在变量中
`include_directories(dir)` 在目录中寻找头文件
`add_library(lib STATIC/SHARED src)` 生成库
`set_target_properties` 设置目标属性
`add_executable` 生成可执行文件
`find_library` 查找库
`target_link_Libraries` 将库链接到可执行文件
`install` 安装配置

系统变量
`CMAKE_BUILD_TYPE` 编译类型
`PROJECT_BINARY_DIR` 执行 cmake 命令的目录
`PROJECT_SOURCE_DIR` 工程根目录
`LIBRARY_OUTPUT_PATH` 库文件生成目录
`EXECUTABLE_OUTPUT_PATH` 可执行文件生成目录
`CMAKE_INSTALL_PREFIX` 安装路径前缀，DESTINATION 指定绝对路径时无效

```shell
$ cat CMakeLists.txt 
cmake_minimum_required(VERSION 2.8)

project(cmake_demo)

set(CMAKE_BUILD_TYPE Debug)

add_compile_options(-std=c++11 -Wall -O2)

# add_executable(demo main.cpp hello.cpp)

# aux_source_directory(./src SRC)
aux_source_directory(. MAIN)

include_directories(./include)

# add_library(hello_shared SHARED ${SRC})
# add_library(hello_static STATIC ${SRC})

# set_target_properties(hello_shared PROPERTIES OUTPUT_NAME "hello_shared")
# set_target_properties(hello_static PROPERTIES OUTPUT_NAME "hello_static")

# set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/../lib)
# set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

# add_executable(demo ${MAIN} ${SRC})

find_library(LIB hello_shared ${PROJECT_SOURCE_DIR}/lib)

set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR})

add_executable(demo ${MAIN})

target_link_Libraries(demo ${LIB})

set(CMAKE_INSTALL_PREFIX ${PROJECT_SOURCE_DIR})
install(TARGETS demo DESTINATION ${PROJECT_SOURCE_DIR}/install/)
```

```shell
$ tree .
.
├── build
│   ├── CMakeCache.txt
│   ├── CMakeFiles
│   ├── cmake_install.cmake
│   └── Makefile
├── CMakeLists.txt
├── demo
├── include
│   └── hello.h
├── lib
│   ├── libhello_shared.so
│   └── libhello_static.a
├── main.cpp
└── src
    └── hello.cpp

5 directories, 10 files
```


#### 参考

https://blog.csdn.net/iuu77/article/details/129229361

