# 移动User目录

## 由于没有复制软连接，会出现一些bug

```batch
robocopy "C:\\Users" "F:\\Users" /E /COPYALL /XJ 
rmdir "C:\\Users" /S /Q
mklink /J "C:\\Users" "F:\\Users"
```

robocopy 选项/XD 可排除文件夹，不进行复制

/XJ选项表示不复制软连接（复制软连接的话，会复制软连接的实体，而不是只有软连接，就会导致递归复制，文件夹无限嵌套），如果自己设置了软连接，需要重新设置

上面的内容保存为 mv.bat 保存到D盘。

开始运行或徽标+R

shutdown -O -r -t 0

重启到恢复界面

进入疑难问题菜单-命令提示符

CD D:

执行MV.bat 即可.

# 软链接（符号链接）和硬链接

对软链接的删除操作不会影响被链接对象，只是删除该快捷方式

而对硬链接的所有操作和对链接对象进行操作无异

* 创建文件软链接
  
  ```batch
  mklink Link Target
  ```

* 创建目录软链接
  
  ```batch
  mklink /D Link Target
  ```

* 创建文件硬链接
  
  ```batch
  mklink /H Link Target
  ```

* 创建目录硬链接
  
  ```batch
  mklink /J Link Target
  ```

# 共享网络

* 选择有网的网卡，如WLAN
  
  1. 右键，属性 
  
  2. 选择“共享”选项卡
  
  3. 勾选“允许其他网络用户......”
  
  4. 家庭网络链接
     
     选择和要共享网络的电脑连接了的网卡名称，如”以太网“
  
  5. 确定

* 查看被共享网卡的ip，如上所说的”以太网“
  
  该网卡的IP应和WLAN保持一致，如果IP地址设置错误了，即使保存了再进去属性看IPv4地址，也是被清空了的

# wsl

## 网络代理

1. 网络模式改为VirtioProxy

2. wsl内设置代理
   
   ```bash
   # 注意：VirtioProxy时wsl的ip和主机ip是一样的，但是用该ip访问不了代理，
   # 这里要用0.0.0.0进行访问
   export http_proxy=http://0.0.0.0:20808
   export https_proxy=http://0.0.0.0:20808
   export all_proxy=http://0.0.0.0:20808
   ```

3. 可以将上诉脚本写入.bashrc中