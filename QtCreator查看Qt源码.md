# ~~QtCreate查看Qt源码~~

全都没有配置完全成功qtbase.pro勉强能用，但是.h到.cpp 的函数定义跳转不能用

还不如code browser方便，启动的时候还需要解析pro，有点慢

等以后有机会继续弄吧

。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。。

## Qt所有组件

Qt助手搜All Modules

## 使用qtbase.pro

编译命令：

```batch
configure.bat -prefix D:\Qt\5.15.2\build_qtbase -debug -platform win32-msvc -opensource -confirm-license -nomake tests -skip qtwebengine -qt-zlib -ssl -icu -opengl desktop
```

建议只配置qtbase.pro，qt.pro太大了，也不好配置

* 打开qtbase.pro

* 在构建步骤中添加qmake额外参数
  
  ```qmake
  -opensource -confirm-license -nomake tests -skip qtwebengine -qt-zlib -ssl -icu -opengl desktop
  ```

* 不要执行qmake，让其自动解析

## 使用qt.pro

* 选择构建套件时注意**改构建目录**，默认构建目录和构建套件在同一个目录，会弄乱构建套件

* 跳过不需要的组件
  
  所有组件的名字可以在Src/.gitmodules文件中查看
  
  更改qt.pro中的QT_SKIP_MODULES
  
  ```qmake
  QT_SKIP_MODULES = qtvirtualkeyboard qtgamepad qtserialbus qtserialport qtwayland qtmacextras qtx11extras qt3d qtcanvas3d \
    qtpurchasing qtconnectivity qtandroidextras \
    qtwebengine qtwebview qtwebchannel qtwebglplugin qtwebsockets\
    qtquicktimeline qtquick3d qtlocation qtquickcontrols qtquickcontrols2 qtopcua qtlottie qtscxml qtpim qtdocgallery qtfeedback qtgraphicaleffects qtdeclarative \
    qtdoc qtscript qttranslations qttools \
    qtsensors
  ```

* qmake过程会询问很多次开源/商用选择，以及开源协议确认

* qmake过程中出现QMAKE_MSC_VER isn't set
  
  在构建套件的环境变量中增加该环境变量，对应值如下：
  
  所有版本号均为该代最低版本（不能直接使用），因此具体值还是要查询自己当前安装的版本为多少
  
  如果不需要编译，只是查看代码，可以直接使用
  
  | msvc | Visual C++ | MSVC_VER | QMAKE_MSC_VER |
  | ---- | ---------- | -------- | ------------- |
  | 2008 | 15.0       | 9.0      | 1500          |
  | 2010 | 16.0       | 10.0     | 1600          |
  | 2012 | 17.0       | 11.0     | 1700          |
  | 2013 | 18.0       | 12.0     | 1800          |
  | 2015 | 19.0       | 14.0     | 1900          |
  | 2017 | 19.10      | 15.0     | 1910          |
  | 2019 | 19.20      | 16.0     | 1920          |

* 只要qt项目前的正在进行标志还在转，或者右下角的qmake进度条还没走完，即使报错也不要停，最后会加载出来的，因为不需要编译，所以不用管这些报错

* qmake过程还可能导致QtCreator无响应，等就完了，时间比较久

* xctest.prf报Cannot find feature sdk错误
  
  xctest是XCode自带的测试框架（Mac、iOS）
