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

# 变量

* 注意：变量名字是大小写敏感的

* 定义
  
  ```cmake
  set(Value "dlkfjl")
  ```

* 引用
  
  使用${}进行引用
  
  ```cmake
  message("curl_library" ${CURL_LIBRARAY})
  ```

# CMakePresets.json和CMakeUserPresets.json

[cmake-presets(7) — CMake 4.3.1 Documentation](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html)

需要cmake 3.19以上

这两个是工程配置文件，前者是与本地环境无关的部分，后者是本地相关的部分

前者会被项目跟踪，而后者不会，一般从前者自动生成，和.pro与.pro.user文件间的关系相似

CMakePresets 支持 configure、build、test、package 几个阶段

不要使用相对路径（./build)，使用\${sourceDir}进行引用

CMakePresets.json示例

```json
{
  "version": 3,
  "cmakeMinimumRequired": {
    "major": 3,
    "minor": 19,
    "patch": 0
  },
  "configurePresets": [
    {
      "name": "macos",
      "hidden": true,
      "condition": {
        "type": "equals",
        "lhs": "${hostSystemName}",
        "rhs": "Darwin"
      },
      "generator": "Xcode",
      "warnings": {"dev": true, "deprecated": true},
      "cacheVariables": {
        "BUILD_TESTING": "OFF"
      }
    },
    {
      "name": "darwin-debug",
      "inherits": "macos",
      "displayName": "Darwin 10.14+ (Debug)",
      "description": "NetEase MSS C wrapper for macOS - Debug Configuration",
      "binaryDir": "${sourceDir}/build",
      "cacheVariables": {
        "BUILD_TESTING": "ON",
        "CMAKE_BUILD_TYPE": "Debug",
        "CMAKE_INSTALL_PREFIX": "${sourceDir}/darwin-debug"
      }
    }
  ],
  "buildPresets": [
    {
      "name": "darwin-debug",
      "configurePreset": "darwin-debug",
      "displayName": "Darwin Local Compilation (Debug)",
      "description": "NetEase MSS C wrapper for macOS - Debug Configuration",
      "configuration": "Debug"
    },
    {
      "name": "darwin-release-x86_64",
      "configurePreset": "darwin-release-x86_64",
      "displayName": "Darwin x86_64 Local Compilation (Release)",
      "description": "NetEase MSS C wrapper for macOS x86_64 - Release Configuration",
      "configuration": "Release",
      "targets": ["install"]
    },
```

# .in文件生成源代码文件

cmake可以根据配置将.in生成最终的源代码文件

# cmake函数

## find_package 模块查找

* find_package()函数会在CMAKE_PREFIX_PATH目录中查找模块文件。
  
  CMAKE_PREFIX_PATH可以用环境变量设置。

boost的cmake模块文件为BoostConfig.cmake

* find_package()有三种模式，常用有两种模式：模块模式、配置模式
  
  两种模式查找的文件：
  
  * 模块模式：
    
    Find<PackageName>.cmake
  
  * 配置模式
    
    - <lowercasePackageName>-config.cmake
    
    - <PackageName>Config.cmake
    
    - <PackageName>.cps
    
    - <lowercasePackageName>.cps

* 如果找到了包，则会定义PackageName_Found的变量
  
  注意：find_package(packageName)的参数是不分大小写的，但是PackageName_Found这个变量的名字是分大小写的
  
  ```cmake
  find_package(levelDB) # 如果找到，会生成levelDB_Found变量，实际的文件名是leveldb-config.cmake，也是可以找到
  if(levelDB_Found) # 有
  endif()
  if(LEVELDB_Found) # 没有
  endif()
  ```

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

# 函数和宏

## 函数 function

```cmake
function(function_name arg1 arg2 ...)
    # function body
endfunction()
```

函数可以有返回值，用set配合PARENT_SCOPE实现

```cmake
function(add_numbers num1 num2 result_var)
    math(EXPR result "${num1} + ${num2}")
    set(${result_var} ${result} PARENT_SCOPE) # 使用PARENT_SCOPE将值传递回调用位置
endfunction()
```

## 宏 macro

宏没有返回值

```cmake
macro(PRINT_HELLO_WORLD)
    message("HELLO WORLD!")
endmacro()

macro(PRINT_MESSAGE my_message)
  message("${my_message}")  # 使用宏参数
  set(var_in_macro "Inside Macro")  # 在宏中定义变量
endmacro()
```

## 函数和宏的预定义变量

- **ARGC**：参数总数

- **ARGV**：所有参数列表

- **ARGN**：超出命名参数后的可选参数列表

- **ARGV0, ARGV1...**：按索引访问具体参数值

## 参数解析

可以使用cmake_parse_arguments()进行批量的参数解析

```cmake
cmake_parse_arguments(<prefix> <options> <one_value_keywords> <multi_value_keywords> <args>...)
```

<prefix>是解析后的变量前缀，会通过下划线与其他Key名相连；
<options>表明当前Key名都是option类 型的
<one_value_keywords>表明当前Key名中只存在一个变量
<multi_value_keywords>表面当前Key名中可存在多个变量

示例：

```cmake
function(my_function2 FunctionTarget)
    set(options ENABLE_PRINT EANBLE_SHOW)
    set(oneValueArs PRINT_VALUE)
    set(multiValueArgs SUB_VALUES DEFUALT_VALUES)
    message(STATUS "function target:${FunctionTarget}")
    cmake_parse_arguments(FUNC "${options}" "${oneValueArgs}" "${multiValueArgs}" ${ARGN})
    message(STATUS "ENABLE_PRINT:${FUNC_ENABLE_PRINT}")
    message(STATUS "ENABLE_SHOW:${FUNC_EANBLE_SHOW}")
    message(STATUS "PRINT_VALUE:${FUNC_PRINT_VALUE}")
    foreach(sub ${FUNC_SUB_VALUES})
        message(STATUS "sub value:${sub}")
    endforeach()
    foreach(default ${FUNC_DEFUALT_VALUES})
        message(STATUS "default value:${default}")
    endforeach()
endfunction(my_function2 FunctionTarget)

# 调用
my_function2(test_target
    ENABLE_PRINT  # option参数给就是TRUE，不给就是FALSE
    EANBLE_SHOW  
    PRINT_VALUE helloworld
    SUB_VALUES one two three
    DEFUALT_VALUES ha wa gou so )
```

## 函数和宏的区别

* 函数的作用方式是调用，获取返回值，使用的是值传递
  
  宏的作用方式是字符替换

* 函数中定义的变量作用域只在函数内，宏中定义的变量作用域是全局

* 函数可以有返回值，宏没有返回值

# 构建和编译

## 构建编译流程

* 构建工程
  
  路径变量值有多个路径用;（分号）分隔
  
  ```bash
  mkdir build
  cd build
  cmake .. -G "Visual Studio 18 2026" -A x64 -DCMAKE_INSTALL_PREFIX=C:\Install
  ```
  
  * -DCMAKE_CXX_FLAGS="/MP"
    
    可以指定msbuild的/MP选项，也可以查看CMakeList.txt内使用多线程编译需要的条件
  
  * CMAKE_INSTALL_PREFIX在环境变量中定义也能生效，linux不能生效，要在命令行指定

* 编译
  
  ```bash
  cmake --build . --config RelWithDebInfo --target INSTALL
  ```
  
  * 为编译器指定参数
    
    ```bash
    cmake --build . --config Release -- /p:CL_MP=true
    ```
    
    msbuild中指定参数时/和-是一样的，make中可能不需要用--进行
    
    ```bash
    cmake --build . --config Release -j
    ```
  
  * cmake --build通常就相当于msbuild或make或ninja
- 常用流程
  
  ```bash
  cmake -B builddir -DCMAKE_BUILD_TYPE=Release -DCMAKE_PREFIX_PATH= -DCMAKE_INSTALL_PREFIX=
  # 前面指定了CMAKE_BUILD_TYPE后这的--config选项可以不要
  # -- 后面的参数原样传给编译工具，这个-j 16是传给make的多线程编译选项
  cmake --build builddir --config Release -- -j 16
  ```

## 常用构建选项

```bash
--build build-dir
--install build-dir
--config [Debug | Release | ReleaseWithDebugInfo]
-DCMAKE_PREFIX_PATH=
-DCMAKE_INSTALL_PREFIX=
-DCMAKE_BUILD_TYPE=
```

## 编译器选项

### Release Debug

```batch
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
```

### 编译选项

CMAKE_C_FLAGS

CMAKE_CXX_FLAGS

### 链接选项

CMAKE_SHARED_LINKER_FLAGS

CMAKE_EXE_LINKER_FLAGS

# 多配置编译安装到不同目录

有两种方式：使用--install或使用两个不同的build目录

1. 使用--install，需要多配置编译器
   
   ninja和make不行，因为他们一个build目录只能编译一种配置
   
   ```bash
   # 构建时如果需要两种配置的依赖库，可以同时传入
   cmake -B build "-DCMAKE_PREFIX_PATH=$PWD/../install/Debug;$PWD/../install/Release" 
   
   cmake --build build --config Debug
   cmake --build build --config Release
   
   cmake --install build --config Debug --prefix $(pwd)/../install/Debug
   cmake --install build --config Release --prefix $(pwd)/../install/Release
   ```

2. 使用两个不同的build目录
   
   ```bash
   cmake -B build-debug -DCMAKE_BUILD_TYPE=Debug -DCMAKE_INSTALL_PREFIX=$(pwd)/../install/Debug
   cmake -B build-release -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=$(pwd)/../install/Release
   
   cmake --build build-debug
   cmake --install build-debug
   
   cmake --build build-release
   cmake --install build-release
   ```
