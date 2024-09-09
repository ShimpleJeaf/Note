# makefile

```makefile
.SUFFIXES:.cpp

# 编译
.cpp.o:
# 或%.o:%.cpp    用name.o:name.cpp后面的连接命令不运行（不知为何）
# 执行的命令前必须有一个tab
    g++ -c [-g] [-cflags] [-fPIC] -Ddefine -Iincludepath -o $@ $(@:.o=.cpp)
    # -g    添加调试信息
    # -fPIC 动态库必须加

# 链接库
libname.so:objects.o
    g++ [-shared] -o libname.so objects.o -Llibpath -llibname
    # -shared 动态库

# 链接可执行文件
program.linux:objects.o
    g++ -o program.linux objects.o -Llibpath -llibname

# windows
    g++ name.dll|libname.so -o main.exe main.o
```

    

# tcpdump

dump traffic on network

-D              列出所有可用的网卡及其编号

-i <u>interface</u>    指定网卡，自己发给自己的到本地回环网卡lo抓

-c <u>count</u>        制定抓包数

-w <u>file</u>         将抓包的数据写入文件

-r <u>file</u>         读取文件并解包

-v -vv -vvv     打印协议头信息和包总长（包括协议头）

**打印内容**

-x              hex，包头

-xx             hex，包头，链路层包头

-X              hex，ASCII，header of each packet

-XX             hex，ASCII，header of each packet

udp header: 8btye + 1 Data: n byte

udp[begin:len] 取udp的begin字节开始的len个字节作为一个值（大端字节序）

    len: 1,2,4

    begin可超出udp header的实际长度

例:

    UDP

    0x01 0x23 0x45 0x67 0x89 0x10

    'udp[2:2] == 0x5678'

    'udp[2:4] = 0x45678910'

    

# tar

GNU 'tar' saves many files together into a single tape or disk archive, and can restore individual files from the archive.

-c          创建打包文件

-x          解压缩

-t          查看打包内容

-z          gzip    *.tar.gz

-j          bzip2   *.tar.bz2

-J          xz      *.tar.xz

-v          解压或压缩时显示正在处理的文件

-f <u>filename</u> -f后立即跟将被处理的文件

-C <u>path</u>     指定解压目录

-p          保留原本权限和属性

-P          保留绝对路径，解压时到原有路径，不要用为好

--exclude=FILE  不要打包的文件

# zip

package and compress (archive) files

-r          递归包含文件夹

-e          加密码

uzip        解压

        

# ssh

OpenSSH SSH client (remote login program)

ssh name@host < test.sh

ssh name@host "mkdir a； rm -r a" 

    远程执行命令脚本，两条命令间用‘；’分割

##### 免密登录

ssh-keygen 生成密钥

ssh-copy-id username@host 上传密钥

或 cat id_rsa.pub >> ~/.ssh/authorized_keys

        公钥

chmod 600 authorized_keys

chmod 700 -R .ssh

        

# lsof

list open files

-i [<u>i</u>] :<u>port</u>     查看端口占用

[<u>names</u>]          显示开启文件的进程

-c <u>c</u>             显示进程现在打开的文件

-c -p <u>s</u>          列出进程号为s的进程所打开的文件

-g [<u>s</u>]           显示归属PGID为s的进程情况

-d <u>d</u>             显示fd为d的进程

+d <u>d</u>             显示目录d下被进程开启的文件

+D <u>D</u>             同上，但是会递归搜索目录，时间较长

-i -U            显示所有打开的端口和UNIX domin文件

        

# netstat

Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships

netstat -tunlp | grep <u>port</u>

-t 仅显示tcp相关选项

-u 仅显示udp相关选项

-n 拒绝显示别号，能显示数字的全部转化为数字

-l 仅列出在Listen监听的服务状态

-p 显示建立相关链接的程序名

      

# Bash脚本

@<u>command</u>     不显示命令，只显示结果

$(<u>command</u>)   引用命令的输出

\`<u>command</u>\`    引用命令的输出，用上一种更好，这种可能会因为符号\`导致冲突

$<u>var</u>         引用变量

例：

var1=$(ls)

var2=$var1

##### 远程脚本

ssh user@host "cd /home; touch a.txt"

或

```bash
#!/bin/bash
ssh user@host << eof
cd /home
touch a.txt
exit
eof
echo "done"
```

执行上面的脚本可能会打印stdin not terminal，实际是执行成功了的，可以用ssh -tt强制终端模式解决。

    

# yum

Cent OS 和 RedHat

* 库源配置
  
  1. 配置文件
     
     /etc/yum.repos.d/local.repo
     
     ```ini
     [local]                # 仓库类别
     name=local             # 名称
     baseurl=file:///mnt    # 源指向路径，可以是本地镜像或网址
     enable=1               # 启用
     gpgcheck=0             # 关闭签名检测
     ```
     
     一个配置只能挂一个iso，如果要增加镜像路径，[local]和name两个字段都要改
  
  2. 配置好文件后
     
     ```shell
     yum clean all
     yum makecache
     ```

* 命令

例：yum install gcc

install         安装

    --setopt=protected-multilib=false gcc 提示多库版本保护时使用

remove          卸载

list installed  列出已安装的软件

     updates    可更新的软件

     available  可安装的软件

upgrade         升级包

groupupdate     升级程序组

info            显示信息

groupinfo       显示程序组信息

groupremove     删除程序组

deplist         查看依赖

grouplist

groupinstall

    --skip-broken 跳过有问题的包

# Qt

## 用windeployqt工具对Qt程序进行打包

**Windeployqt**

```batch
cd $QTDIR\bin\
windeployqt.exe D:\a\xx.exe
:: 或者：
cd D:\a\
$QTDIR\bin\windeployqt.exe xx.exe
:: 所有依赖的Qt动态库都会复制到xx.exe所在目录下
```

xx.exe依赖的所有动态库都会拷贝到xx.exe所在的目录下

**注意**：依赖的mingw编译器的动态库不会复制，运行xx.exe更新错误提示复制缺的动态库就行了。通常下面三个动态库都是必须的。

> libgcc_s_seh-1.dll
> libstdc++-6.dll
> libwinpthread-1.dll

**特别注意**：缺libwinpthread-1.dll库不会报缺库错误，而是报“应用程序无法正常启动（0xc000007b）”错误。

可选项：

```
Options:
  -?, -h, --help            Displays this help.
  -v, --version             Displays version information.  --dir <directory>         Use directory instead of binary directory.
  --libdir <path>           Copy libraries to path.
  --plugindir <path>        Copy plugins to path.
  --debug                   Assume debug binaries.
  --release                 Assume release binaries.
  --pdb                     Deploy .pdb files (MSVC).
  --force                   Force updating files.
  --dry-run                 Simulation mode. Behave normally, but do not
                            copy/update any files.
  --no-patchqt              Do not patch the Qt5Core library.
  --no-plugins              Skip plugin deployment.
  --no-libraries            Skip library deployment.
  --qmldir <directory>      Scan for QML-imports starting from directory.
  --no-quick-import         Skip deployment of Qt Quick imports.
  --no-translations         Skip deployment of translations.
  --no-system-d3d-compiler  Skip deployment of the system D3D compiler.
  --compiler-runtime        Deploy compiler runtime (Desktop only).
  --no-compiler-runtime     Do not deploy compiler runtime (Desktop only).
  --webkit2                 Deployment of WebKit2 (web process).
  --no-webkit2              Skip deployment of WebKit2.
  --json                    Print to stdout in JSON format.
  --angle                   Force deployment of ANGLE.
  --no-angle                Disable deployment of ANGLE.
  --no-opengl-sw            Do not deploy the software rasterizer library.
  --list <option>           Print only the names of the files copied.
                            Available options:
                             source:   absolute path of the source files
                             target:   absolute path of the target files
                             relative: paths of the target files, relative
                                       to the target directory
                             mapping:  outputs the source and the relative
                                       target, suitable for use within an
                                       Appx mapping file
  --verbose <level>         Verbose level (0-2).
```

## 翻译（国际化）

1. 生成ts文件
   
   **QtCreator**
* 在pro中加上TRANSLATIONS设置，如TRANSLATIONS += trans_zh_CN.ts，可以设置多个，每个对应生成的ts文件名

* 并在Qt Creator中点击菜单栏-工具-外部-Qt语言家-更新翻译，默认在pro目录生成对应的ts文件
  
   **VS**

* 扩展-Qt VS Tools-Create New Translation File

* 右键新生成的ts文件，选择lupdate
2. 翻译ts文件
   
   使用Qt目录下的Linguist软件或文本编辑器翻译ts文件

3. 生成qm文件
   
   点击Qt Linguist文件-保存，再点文件-发布，
   
   或者保存后点QtCreator菜单栏-工具-外部-Qt语言家-发布翻译，
   
   或者VS右键保存的ts文件，选择lrelease

4. 使用翻译文件
   
   ```cpp
   QApplication app(argc, argv);
   QTranslator translator;
   translator.load("trans_zh_CN.qm");
   // 按系统设置自动加载不同的翻译文件，第二个参数是qm文件的相对目录
   translator.load("trans_" + QLocale::system().name(), "translations");
   app.installTranslator(&translator);
   ```

# clang-uml

clang-uml -c config.yaml

配置说明：[clang-uml/docs/configuration_file.md at master · bkryza/clang-uml (github.com)](https://github.com/bkryza/clang-uml/blob/master/docs/configuration_file.md)

**找不到头文件**

config.yaml中的add_compile_flags:添加-I头文件目录选项

# PlantUML QEditor
