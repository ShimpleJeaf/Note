# boost

# Windows编译安装boost库

boost版本：1.80.0

## 1. 用MSVC编译boost

编译工具：MSVC-143

vs版本号对应：

[Visual Studio版本号对应表 - ChrisZZ - 博客园](https://www.cnblogs.com/zjutzz/p/6013783.html)

[MSVC_TOOLSET_VERSION — CMake 4.1.0 Documentation](https://cmake.org/cmake/help/latest/variable/MSVC_TOOLSET_VERSION.html)

### 1.1. 编译b2.exe

```bat
bootstrap.bat msvc
```

* 可指定gcc、msvc 、vcxxx，指定msvc时自动查找可用编译器，vc141指定特定版本编辑器

* 如果报错cl命令找不到，就用vs 的命令行工具，切换到boost目录执行，x64 Native Tools Command Prompt for VS 2022

### 1.2. 编译boost

```bat
b2.exe install
```

注意：当你用vs2022最新工具编译老版本的boost时可能会出现b2程序识别不了vs编译器版本的问题
现象如下，什么都识别不到：

```bat
Performing configuration checks

    - default address-model    : none [1]
    - default architecture     : none [1]
    - x86                      : no [2]
    - arm                      : no [2]
    - mips1                    : no [2]
    - power                    : no [2]
    - sparc                    : no [2]
    - has synchronization.lib  : no [2]
```

原因是vs编译工具版本号v14.4x实际上依然是v143版本，导致b2错误识别，详情请参考：[https://github.com/bfgroup/b2/issues/391](https://github.com/bfgroup/b2/issues/391)

可以按如下方法修改b2的源码，运行bootstrap.bat重新编译b2.exe，再编译boost就可以了。
源码位置：boost_1_80_0\tools\build\src\tools\msvc.jam
行号不一定正确，建议搜索内容。

```jam
diff --git a/src/tools/msvc.jam b/src/tools/msvc.jam
index f56f1cedb..7862e9988 100644
--- a/src/tools/msvc.jam
+++ b/src/tools/msvc.jam
@@ -1119,7 +1119,7 @@ local rule generate-setup-cmd ( version : command : parent : options * : cpu : g
         }
         else
         {
-            if [ MATCH "(14.3)" : $(version) ]
+            if [ MATCH "(14.[34])" : $(version) ]
             {
                 if $(.debug-configuration)
                 {
```

## 2. 用MinGW编译

首先要保证MingGW的g++编译器目录在PATH内，编译b2.exe和boost库都需要

### 2.1. 编译b2.exe

切换到build目录并编译b2.exe

```bat
cd tools\build\src\engine
build.bat mingw
```

然后将编译出的b2.exe和bjam.exe复制到boost主目录下，bjam.exe在新版中可能没有。

### 2.2. 编译boost

```bat
b2.exe install toolset=gcc
```

## 3. b2.exe选项参考

b2的参数参考：

```bat
b2 [options] [properties] [install|stage]
选项:
  install                 安装头文件和编译完成的库到配置的位置（如下）
  =======                 
  --prefix=<PREFIX>       安装到PREFIX
                          默认值：Windows：C:\Boost
                          默认值：Unix，Linux等：/usr/local

  --libdir=<LIBDIR>       将库安装到LIBDIR
                          默认值：<EPREFIX>/lib

  --includedir=<HDRDIR>   将头文件安装到HDRDIR
                          默认值：<PREFIX>/include


  stage                   构建并安装编译完成的库文件到stage目录
  =====                   
  --stagedir=<STAGEDIR>   安装库文件到该位置
                          默认值：./stage


  --show-libraries        显示需要构建、安装步骤的Boost库

  --with-<library>        只构建和安装指定的库<library>

  --without-<library>     不构建、存储或安装指定的库<library>
                          默认构建所有的库

属性配置:
  toolset=toolset         显示指定工具链构建

  variant=debug|release   构建debug或release库

  link=static|shared      构建静态库或动态库

  threading=single|multi  构建单线程或多线程库

  runtime-link=static|shared
                          链接C和C++的静态库或动态库

重要选项:

  * --clean 移除目标而不是构建
  * -a 重新构建所有库
  * -n 不执行命令，而只是打印它们
  * -d+2 显示执行的命令
  * -d0 不显示所有information信息
  * -q 遇到第一个错误就停止
  * --reconfigure Rerun all configuration checks
  * --debug-configuration 诊断配置
  * --debug-building 报告每个目标用什么属性构建的
  * --debug-generator Diagnose generator search/execution
```

```bat
b2 [options] [properties] [install|stage]

Options:

  install                 Install headers and compiled library files to the
  =======                 configured locations (below).

  --prefix=<PREFIX>       Install architecture independent files here.
                          Default: C:\Boost on Windows
                          Default: /usr/local on Unix, Linux, etc.

  --libdir=<LIBDIR>       Install library files here.
                          Default: <EPREFIX>/lib

  --includedir=<HDRDIR>   Install header files here.
                          Default: <PREFIX>/include

  stage                   Build and install only compiled library files to the
  =====                   stage directory.

  --stagedir=<STAGEDIR>   Install library files here
                          Default: ./stage

  --show-libraries        Display the list of Boost libraries that require
                          build and installation steps, and then exit.           

  --with-<library>        Build and install the specified <library>. If this
                          option is used, only libraries specified using this
                          option will be built.

  --without-<library>     Do not build, stage, or install the specified
                          <library>. By default, all libraries are built.

Properties:

  toolset=toolset         Indicate the toolset to build with.

  variant=debug|release   Select the build variant

  link=static|shared      Whether to build static or shared libraries

  threading=single|multi  Whether to build single or multithreaded binaries

  runtime-link=static|shared
                          Whether to link to static or shared C and C++
                          runtime.

Important Options:

  * --clean Remove targets instead of building
  * -a Rebuild everything
  * -n Don't execute the commands, only print them
  * -d+2 Show commands as they are executed
  * -d0 Suppress all informational messages
  * -q Stop at first error
  * --reconfigure Rerun all configuration checks
  * --debug-configuration Diagnose configuration
  * --debug-building Report which targets are built with what properties
  * --debug-generator Diagnose generator search/execution
```

# 单元测试

```cpp
// 1. 定义测试用例main函数，BoostStudyTest是测试名字，可以为空
// 必须在包含单元测试头文件之前定义，且只能定义一次
#define BOOST_TEST_MODULE BoostStudyTest

// 2. boost单元测试有三种使用方式：仅头文件、静态链接、动态链接
// 2.1 仅头文件
#include <boost/test/included/unit_test.hpp>
// 2.2 静态链接
#include <boost/test/unit_test.hpp>
// 2.3 动态链接
#define BOOST_TEST_DYN_LINK
#include <boost/test/unit_test.hpp>

// 包含待测试的目标文件，如果是测试库，可以通过静态或动态链接的方式进行
// 也可以通过直接包含cpp文件的方式进行，看情况选用
#include "interpreter.cpp"

// 测试对象：Interpreter类

// 3. 测试套件夹具，就是测试套件用的全局变量
// 另外还有测试用例夹具
// 夹具构造在每个测试用例运行前执行，夹具析构在每个测试用例运行完后执行
struct interpreter_fixture
{
    interpreter_fixture()
    {
        valueMap["a"] = 2;
        valueMap["b"] = 3;
        valueMap["c"] = 4;
        valueMap["d"] = 5;
    }
    ~interpreter_fixture() {}
    map<string, double> valueMap;
};

// 4. 测试套件
// 测试套件的起始位置，要以BOOST_AUTO_TEST_SUITE_END()结尾，将测试用例括在中间
// 有测试夹具的声明方式，第一个参数是测试套件名字，第二个参数是测试套件夹具类名
BOOST_FIXTURE_TEST_SUITE(suite_interpreter, interpreter_fixture)
// 无测试夹具的声明方式
BOOST_AUTO_TEST_SUITE(suite_interpreter)

// 5. 测试用例
BOOST_AUTO_TEST_CASE(test_find_first_char)
{
    string str = "a+b*c/d-a";
    char c;
    auto pos = find_first_char(str, "*/", c);
    BOOST_CHECK_EQUAL(pos, 3);
    BOOST_CHECK_EQUAL(c, '*');
    pos = find_first_char(str, "/*", c);
    BOOST_CHECK_EQUAL(pos, 3);
    BOOST_CHECK_EQUAL(c, '*');
    pos = find_first_char(str, "+-", c);
    BOOST_CHECK_EQUAL(pos, 1);
    BOOST_CHECK_EQUAL(c, '+');
    pos = find_first_char(str, "-+", c);
    BOOST_CHECK_EQUAL(pos, 1);
    BOOST_CHECK_EQUAL(c, '+');
    pos = find_first_char(str, "72934", c);
    BOOST_CHECK_EQUAL(pos, string::npos);
}

// 5.1 测试用例夹具
struct valueinterpreter_fixture
{
    int ta;
    valueinterpreter_fixture() { ta = 3; }
    ~valueinterpreter_fixture() {}
}
// 有测试用例夹具时的测试用例声明，第一个参数是测试用例名字，第二个参数是测试用例夹具类名
BOOST_FIXTURE_TEST_CASE(test_ValueInterpreter, valueinterpreter_fixture)
// 没有测试用例夹具时的测试用例声明
BOOST_AUTO_TEST_CASE(test_ValueInterpreter)
{
    string str = "a";
    auto inter = Interpreter::analyse(str);
    BOOST_CHECK_CLOSE(inter->interpret(valueMap), 2, 0.0001);
    delete inter;
}

// 测试套件结束
BOOST_AUTO_TEST_SUITE_END()

// 此外还有针对模板Template的测试用例

// 6. 检查宏
BOOST_<level>                       // bool
BOOST_<level>_BITWISE_EQUAL
BOOST_<level>_EQUAL                 // 等于
BOOST_<level>_EQUAL_COLLECTIONS
BOOST_<level>_CLOSE                 // 浮点数等于
BOOST_<level>_CLOSE_FRACTION
BOOST_<level>_GE                    // 大于等于
BOOST_<level>_GT                    // 大于
BOOST_<level>_LE                    // 小于等于
BOOST_<level>_LT                    // 小于
BOOST_<level>_MESSAGE
BOOST_<level>_NE                    // 不等于
BOOST_<level>_PREDICATE
BOOST_<level>_NO_THROW
BOOST_<level>_THROW
BOOST_<level>_EXCEPTION
BOOST_<level>_SMALL


<level> 分为REQUIRE、CHECK、WARN
REQUIRE 测试失败立即停止测试用例，如果测试失败会影响剩下的测试，可用
CHECK   最常用的模式，测试失败测试用例依然继续
WARN    即使测试失败，测试用例的结果依然是成功，但是会写进日志
```
