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