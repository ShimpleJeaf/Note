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
glxinfo | grep profile
```

看输出中的Max core profile版本，编译osg时DOSG_GL_CONTEXT_VERSION不要大于该值

用GLCORE代替GL3编译osg

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
