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

find_package()函数会在CMAKE_PREFIX_PATH目录中查找模块文件。

CMAKE_PREFIX_PATH可以用环境变量设置。

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

# 构建和编译

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
