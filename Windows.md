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