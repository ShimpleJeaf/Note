# 常用命令

```bash
# pkg包管理
pkg update
pkg upgrade
pkg search
pkg show
pkg install
pkg uninstall
pkg list-all
pkg list-installed
```

```bash
# 图形界面换源
termux-change-repo
```

```bash
# 查看进程
ps aux
# 杀进程
kill PID
```

# mesa硬件加速

## 安装顺序

因为有些包有两种，并且互相冲突，只能卸载一个，再安另一个，顺序不对，会重复安装依赖

1. mesa

2. mesa-vulkan-icd-freedreno

3. xfce4和xfce4-goodies

4. firefox

注意：vulkan-loader-android和mesa-vulkan-icd-freedreno是不兼容的

以下引用自 https://github.com/alexvorxx/zink-xlib-termux

> To use Zink with proprietary Vulkan driver:
> 
> *pkg install vulkan-loader-android*
> 
> To use Zink with Turnip Freedreno driver:
> 
> *pkg install mesa-vulkan-icd-freedreno*
> 
> You also may compile it yourself.

## opengl测试

添加vblank_mode=0环境变量可以解锁帧率限制

* webgl网页测试
  
  webgl samples

* glmark2

## zink硬件加速

**tigervnc无法硬件加速！！！**

termux-x11可以

* 安装
  
  icd: Installable Client Driver
  
  mesa包含两个驱动mesa-vulkan-icd-freedreno（硬件加速，freedreno gpu）和mesa-vulkan-icd-swrast（非硬件加速）
  
  最好安装mesa之前先手动装驱动，再装mesa
  
  ```bash
  pkg install mesa
  # 安装硬件加速的驱动，只支持freedreno
  pkg install mesa-vulkan-icd-freedreno
  ```

* 使用
  
  最新mesa 25.3.3不需要额外的环境变量或其他参数，直接启动xfce4，就是用的turnip了，在xfce4桌面内关于界面可以看到gpu

* 以下是有问题时作为参考的，正常情况下不需要
  
  mesa命令前加MESA_LOADER_DRIVER_OVERRIDE=zink环境变量；
  
  之前的版本mesa-zink使用GALLIUM_DRIVER=zink，需要安装的包也不一样，包是名mesa-zink，而不是mesa
  
  ```bash
  GALLIUM_DRIVER=zink startxfce4
  MESA_LOADER_DRIVER_OVERRIDE=zink startxfce4
  ```
  
  在我的termux上，有mesa-zink 22.0.5和mesa 25.3.3，直接用mesa没有问题。
  
  如果用mesa-zink和对应的驱动mesa-zink-vulkan-icd-freedreno 22.0.5驱动会崩溃，但是换成mesa 25.3.3对应的驱动mesa-vulkan-icd-freedreno反而可以

## termux-x11

* 安装
  
  ```bash
  # 安装仓库
  pkg install x11-repo
  # 安装termux-x11
  pkg install termux-x11
  ```

* 启动
  
  ```bash
  termux-x11 :1
  ```

* 用x11启动软件
  
  ```bash
  termux-x11 -xstartup "firefox"
  ```

## tigervnc

vnc的性能比较差，3D的话用termux-x11比较好

用x11vnc配xrdp也很慢，使用还有坑，要改配置才能脸上，主要是改etc/xrdp/xrdp.ini里的端口，改成vnc的端口号，username要改成na，lib改成libvnc.so，因为原有的xrdp-sesman认证要走PAM，而termux不支持这种验证方式，改完后验证就转vnc去了

* 安装
  
  ```bash
  pkg install tigervnc
  ```

* 启动
  
  ```bash
  vncserver :1
  ```

* 停止
  
  ```bash
  vncserver -kill :1
  ```

## xfce4

* 安装
  
  ```bash
  pkg install xfce4
  ```

* 启动
  
  端口为5901
  
  ```bash
  # 启动xfce4
  export DISPLAY=:1 
  startxfce4
  ```

## mobox安装wine

mobox的wine版本只到9

```bash
curl -s -o ~/x https://raw.githubusercontent.com/olegos2/mobox/main/install && . ~/x
```

# proot-distro

```
proot-distro list
proot-distro install debian
proot-distro login debian
proot-distro login debian --user shimple --shaded-tmp
```

## socat

因为proot-distro的程序是无法绑定到0.0.0.0的，会导致外部无法访问

将proot-distro内的监听端口转发到termux

```bash
pkg install socat

# proot-distro容器端口p1转发到termux端口p2
# p1和p2不能相同
socat TCP-LISTEN:p2,fork TCP:127.0.0.1:p1
```

## debian

### 添加普通用户

```bash
# root用户下
adduser shimple
# 给shimple用户加root权限
# /etc/sudoers，末尾加
shimple ALL=(ALL:ALL) ALL
```

### 移除i386架构包

```bash
# 删除所有i386包
sudo dpkg --purge --force-all $(dpkg --get-selections |awk '/i386/{print $1}')
# 移除i386架构
dpkg --remove-architecture i386
# 查看其他架构
dpkg --print-foreign-architectures
```

# ssh

1. 安装ssh服务
   
   ```bash
   pkg install openssh
   ```

2. 开启服务
   
   ```bash
   sshd
   ```

3. 在termux上查看ip和用户名
   
   ```bash
   ifconfig 
   whoami 
   # u0_a167
   ```

4. 设置账户密码
   
   ```bash
   passwd
   ```

5. 连接
   
   termux上ssh服务的端口是8022

# 中文输入法

如果右上角没有键盘图标，说明ibus服务异常退出了，在终端执行

```bash
ibus-daemon -drx
```

# WindTerm

如果遇到ssh认证闪退

新建会话 > SSH > 验证 > 取消“尝试公钥认证”、“尝试键盘交互认证”、”尝试gssapi-with-mic身份验证“

# 脚本

## 杀用户组进程

```bash
# 杀本用户组的所有进程
pkill -u $(id -u)
```

# 启动termux-x1及xfce

```bash
termux-x11 :1 -xstartup "dbus-launch --exit-with-session xfce4-session"
```

或

```bash
termux-x11 :1 &
env DISPLAY=:1 dbus-launch --exit-with-session xfce4-session
```

如果dbus-launch不能用

```bash
termux-x11 :1 -xstartup "xfce4-session"
```

或

```bash
export TERMUX_X11_XSTARTUP="xfce4-session"
termux-x11 :1
```
