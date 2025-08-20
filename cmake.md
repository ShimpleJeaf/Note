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

# 模块查找

find_package()函数会在CMAKE_PREFIX_PATH目录中查找模块文件。

boost的cmake模块文件为BoostConfig.cmake

# find_path()的搜索路径

find_path()有默认的搜索路径分别是CMAKE_INCLUDE_PATH和CMAKE_LIBRARY_PATH，注意这两个是环境变量，而不是cmake变量。

# Release Debug

```batch
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo
```
