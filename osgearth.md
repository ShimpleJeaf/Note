## 编译说明

https://docs.osgearth.org/en/latest/build.html

## 依赖库

* libgl-dev

* libjpeg-dev

必须：

* build-essential

* libgdal-dev

* libglew-dev

## VMware兼容

```bash
glxinfo | grep version
```

看输出中的Max core profile版本，编译osg时DOSG_GL_CONTEXT_VERSION不要大于该值

最新的VMWareStation Pro 25H2，OpenGL版本在3d加速开启时比3d加速关闭时低。

OpenGL版本与VMWare版本和虚拟机系统均有关系。

推荐使用3.3

如果无法创建graphic context，可以关闭vmware的3d加速功能，或者使用vnc进行连接(vnc不会使用3d加速，所以没问题)

### 官方解决办法，已经过期了

用GLCORE代替GL3编译osg

如果输出的mesa驱动版本小于3.3

就需要在**Windows**设置以下环境变量：

```bat
set OSG_GL_CONTEXT_VERSION=3.3
set MESA_GL_VERSION_OVERRIDE=3.3
```

再用osgearth_version --caps查看mesa版本是否大于等于3.3

## reset到指定版本

git reset --hard osgearth-3.8

OpenSceneGraph-3.6.5

## 编译 osg

```bash
git clone --branch OpenSceneGraph-3.6.5 --depth 1 https://github.com/openscenegraph/OpenSceneGraph.git
cd OpenSceneGraph
mkdir build && cd build
cmake .. -DOPENGL_PROFILE=GL3 -DOSG_GL_CONTEXT_VERSION=4.6
make -j8
sudo make install
```

## 编译osgearth

注意osg编译的Debug库名字默认带d，而osgEarth默认不带d，这会导致osgEarth编译的osgPlugin库加载不上（因为名字不对），需要让osgEarth编译的Debug库名字也带d

```bash
-DCMAKE_BUILD_TYPE=Debug -DCMAKE_DEBUG_POSTFIX=d
```

```bash
git clone --branch osgearth-3.8 --depth 1 https://github.com/gwaldron/osgearth.git
cd osgEarth
mkdir build && cd build
cmake .. -DOSGEARTH_ENABLE_FASTDXT=OFF
make -j8
sudo make install
```

## 运行时配置链接库查找路径

```bash
LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib:/usr/local/lib64
```

## 快捷键

| 快捷键 | 说明              |
| --- | --------------- |
| W   | 切换显示模式，点状、网格、正常 |
| S   | 渲染性能显示          |
| F   | 切换全屏            |

## 配置

1. 制作chm文件：首先下载doxygen和htmlhelp，用doxygen扫描源码目录，生成html文件，再用htmlhelp编译成chm文件。
   （制作不太成功…可能参数得仔细调，找一份osgearth API手册）

2. 高清影像：高程: http://download.osgeo.org/ossim/data/elevation/; 
   高精度纹理
   数据处理：切割：gdal；纹理校正（添加坐标系统）：global-mapper；拼接

3. osgEarth功能：利用纹理和高程动态生成地形（基于CS模式，服务器在响应前应使用osgearth_cache生成缓存，加快访问速度）。
   主要是.earth文件的编写，其中注意了解每个标签和参数的含义（见osgEarth官网）；
   以及程序中利用osgEarth提供的API去访问控制。

4. sharp文件：在.earth文件中添加sharp文件，其中driver选用ogr，标签选用feature。
   
   也可以在程序中通过featureSource类去获取访问。
   其中，地球化：顶点坐标可以通过osgEarth::ECEF::transformAndLocale去转化坐标系。
   Overlay：覆盖到地球上，通过禁用blending解决颜色混合出现的问题。
   此时可以实现点选之类的功能，区分两个模型，一个是地图节点（mapNode），一个是从sharp文件overlay上去的模型（world节点）。其中mapNode来响应事件，然后改变world节点上模型的属性。
   
   查看编辑：arcview

5. 视点、焦点。
   视点：EarthManipulator->getViewPoint
   焦点（鼠标）：viewer->computeIntersections求交
   标签：LabelControl

6. 是

### 高德地图

高德地图 API 使用的坐标体系，为国测局规定的 GCJ-02 坐标系，是国内的标准坐标体系。区别于 GPS 坐标（WGS-84 坐标系），是在其基础上进行了一次加密。

#### 在线

```xml
<map>   
<XYZImage name="mapbox_satellite" driver="xyz">
  <url><![CDATA[http://webst04.is.autonavi.com/appmaptile?lang=zh_cn&size=1&style=6&x={x}&y={y}&z={z}]]></url>
  <profile>spherical-mercator</profile>
</XYZImage>     
</map>
```

#### 离线

不要用file://，识别不了

```xml
<map>   
<XYZImage name="mapbox_satellite" driver="xyz">
    <url>/home/shimple/Projects/osgearthTest/satelite/{z}/{x}/{y}.jpg</url>
    <profile>spherical-mercator</profile>
</XYZImage>     
</map>
```

##### 地图下载工具

* Hxy1992/map-download
  
  图形工具
  
  已下载：全球0-3
  
  中国：0-6

* atlasdatatech/tiler
  
  命令行工具
