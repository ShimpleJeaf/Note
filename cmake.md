# 设置代理

在CMakelist.txt中添加

```cmake
set(ENV{http_proxy} "http://127.0.0.1:8082")
set(ENV{https_proxy} "http://127.0.0.1:8082")
```

或者直接在环境变量中添加这两个变量

# 环境变量

* 在CMakeLists.txt中定义
  
  ```cmake
  set(ENV{<variable>} [<value>])
  ```

# CMakePresets.json和CMakeUserPresets.json

这两个是工程配置文件，前者是与本地环境无关的部分，后者是本地相关的部分

前者会被项目跟踪，而后者不会，一般从前者自动生成，和.pro与.pro.user文件间的关系相似

# .in文件生成源代码文件

cmake可以根据配置将.in生成最终的源代码文件

# cmake函数

## find_package 模块查找

find_package()函数会在CMAKE_PREFIX_PATH目录中查找模块文件。

boost的cmake模块文件为BoostConfig.cmake

## find_path的搜索路径

find_path()有默认的搜索路径分别是CMAKE_INCLUDE_PATH和CMAKE_LIBRARY_PATH，注意这两个是环境变量，而不是cmake变量。

## add_subdirectory

包含CMakeLists.txt的子项目目录

## add_library

使用指定源文件向项目添加库

[add_library — CMake 4.1.0 文档 - CMake 构建系统](https://cmake.com.cn/cmake/help/latest/command/add_library.html)

## message

```cmake
message([STATUS | WARNING | AUTHOR_WARNING | FATAL_ERROR | SEND_ERROR] "message to display" ...)
```

| 类型             | 说明                      |
| -------------- | ----------------------- |
| 无              | 重要消息                    |
| STATUS         | 非重要消息                   |
| WARNING        | cmake警告，会继续执行           |
| AUTHOR_WARNING | cmake警告（dev），会继续执行      |
| SEND_ERROR     | cmake错误，继续执行，但是会跳过生成的步骤 |
| FATAL_ERROR    | cmake错误，终止所有处理过程        |

# 编译器选项

## Release Debug

```batch
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
```

## 编译选项

CMAKE_C_FLAGS

CMAKE_CXX_FLAGS

## 链接选项

CMAKE_SHARED_LINKER_FLAGS

CMAKE_EXE_LINKER_FLAGS
