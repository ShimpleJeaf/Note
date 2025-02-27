# 在VS中使用

* 直接在visual studio installer中安装clang包，很大

* 自己安装，需要配置
  
  1. 安装clang包，包含llvm
  
  2. vs工程属性中code analysis常规中启动clang-tidy，clang-tidy中填写clang-tidy工具目录和要启动的检查。
  
  3. 运行代码分析，等待结果。ps：可以单个文件进行分析

# clang-tidy检查（check）

* 写好的：
  
  *,-llvmlibc-callee-namespace,-llvm-include-order,-llvmlibc-restrict-system-libc-headers,-fuchsia-default-arguments-calls,-misc-include-cleaner,-cppcoreguidelines-avoid-magic-numbers,-readability-magic-numbers,-hicpp-use-auto,-modernize-use-auto,-misc-const-correctness

* 临时目录（无用）：
  
  D:\Program Files\clang+llvm-19.1.0-x86_64-pc-windows-msvc\bin

* .clang-tidy文件：
  
  ```
   ---
  Checks:            '*,-llvmlibc-callee-namespace'
  WarningsAsErrors:  ''
  HeaderFilterRegex: '.*'
  FormatStyle:       'file'
  User:              administrator
  ```
  
  checks中表示要检查的项，*表示所有检查，以“-“开头的为排除，不以“-”开头的为附加，中间用逗号“,”分隔。

* check表，有多个的通常一起报错
  
  | check                                                           | 原因                |
  | --------------------------------------------------------------- | ----------------- |
  | llvm-include-order                                              | 头文件包含没有排序         |
  | fuchsia-default-arguments-calls                                 | 调用函数时使用了默认参数      |
  | misc-include-cleaner                                            | 头文件没有直接包含，而是间接引用的 |
  | cppcoreguidelines-avoid-magic-numbers,readability-magic-numbers | 使用数字字面值           |
  | hicpp-use-auto,modernize-use-auto                               | 初始化时建议使用auto关键字   |
  | misc-const-correctness                                          | 变量可以用const限定      |
