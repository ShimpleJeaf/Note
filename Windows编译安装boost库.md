# Windows编译安装boost库

boost版本：1.80.0

## 1. 用MSVC编译boost

编译工具：MSVC-143

### 1.1. 编译b2.exe

```bat
bootstrap.bat
```

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