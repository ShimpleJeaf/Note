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

# clang-uml

clang-uml -c config.yaml

配置说明：[clang-uml/docs/configuration_file.md at master · bkryza/clang-uml (github.com)](https://github.com/bkryza/clang-uml/blob/master/docs/configuration_file.md)

**找不到头文件**

config.yaml中的add_compile_flags:添加-I头文件目录选项

# netplan修改ip地址

## 查看网络链接方式

```bash
ls /etc/netplan
# 该目录下的.yaml文件
```

```yaml
# 50-cloud-init.yaml
network:
  version: 2
  ethernets:
    enp0s3:
      critical: true
      dhcp-identifier: "mac"
      dhcp4: true
```

## 修改ip

1. 修改netplan配置文件
   
   ```yaml
   network:
    version: 2
    ethernet:
      enp0s3:
        dhcp4: no
        addresses:
          - 192.168.1.123/24
        # gateway4: 192.168.1.1
        routes:
          - to: default
            via: 192.168.1.1
        nameservers:
          addresses:
            - 8.8.8.8
   ```

2. 应用新配置
   
   测试配置文件
   
   ```bash
   sudo netplan try
   ```
   
   ```bash
   sudo netplan generate
   sudo netplan apply
   ```

3. 检查生效情况
   
   ```bash
   ip addr
   ```

## 临时修改ip

```bash
sudo ip addr flush dev enp0s3
sudo ip addr add 192.168.1.123/24 dev enp0s3
sudo ip route add default via 192.168.1.1
```

* ip addr flush是清除原有IP

* 新IP会在下载重启或网卡重启时失效

## 连接无线网络

* 需要以下软件
  
  ```bash
  sudo apt install network-manager wpasupplicant wireless-tools
  ```

* 扫描无线网卡
  
  ```bash
  sudo iwlist wlol scan | grep ESSID
  ```
  
  wlol是网卡名字，ip addr可查看，一般是wl开头的
  
  记录WIFI的名称，例如wlan0

* 配置netplan配置文件
  
  ```yaml
  network:
    version: 2
    ......
  
    wifis:
      wlan0:
      dhcp4: true
      access-points:
        "wifi_name1":
          password: "mypassword"
        "wifi_name2":
          password: "88888888"
  ```

* 应用
  
  ```bash
  sudo netplan try
  ```
  
  ```bash
  sudo netplan generate
  sudo netplan apply
  ```

* 如果连接到了网络，ip addr能看到IP
  
  如果没有连接到网络，用以下命令尝试启动无线网卡
  
  ```bash
  sudo ip link set wlan0 up
  ```

# Ubuntu配置自启动

/etc/systemd/system/
