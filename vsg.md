# 编译

1. 克隆仓库

   vsgImGui和vsgExamples是可选的，看情况编译

   ```bash
   mkdir vsg
   cd vsg
   git clone --depth 1 --branch v1.1.16 https://github.com/vsg-dev/VulkanSceneGraph.git
   git clone --depth 1 --branch v1.1.14 https://github.com/vsg-dev/vsgXchange.git
   git clone --depth 1 --branch v0.8.0 --recurse-submodules https://github.com/vsg-dev/vsgImGui.git
   git clone --depth 1 --branch v1.1.15 https://github.com/vsg-dev/vsgExamples.git
   ```

   vsgQt，这个依赖Qt，所以需要自己手动编译，下面的编译脚本不能用

   ```bash
   git clone --depth 1 --branch v0.5.0 https://github.com/vsg-dev/vsgQt.git
   ```

2. 编译脚本

   写了个脚本，会安装在vsg/install/Debug或Release目录下

   需要更改vulkansdk的实际路径

   可以使用./compile clear 删除build文件夹，重新编译

   ```bash
   set -e
   
   if [ "$#" -ge 1 -a "$1" = "clear" ]; then
           needclear=1
   fi
   
   rootdir=$(pwd)
   
   # 激活vulkansdk
   source $rootdir/vulkansdk/setup-env.sh
   
   installdir=$rootdir/install
   
   compile() {
        cd $rootdir/$1
        if [ "$needclear" -eq 1 ]; then
            rm -rf build/Debug
            rm -rf build/Release
            echo "删除 $1/build"
        else
            cmake -B build/Debug -DCMAKE_INSTALL_PREFIX=$installdir/Debug -DBUILD_SHARED_LIBS=ON
            cmake --build build/Debug --config Debug -j8 --target install
            cmake -B build/Release -DCMAKE_INSTALL_PREFIX=$installdir/Release -DBUILD_SHARED_LIBS=ON
            cmake --build build/Release --config Release -j8 --target install
        fi
   }
   
   compile VulkanSceneGraph
   compile vsgXchange
   compile vsgImGui
   compile vsgExamples
   ```

3. 激活环境

   需要更改vulkansdk的实际路径

   ```bash
   MSG="需要输入一个参数，Debug或Release，将激活对应的库"
   
   if [ "$#" != 1 ]; then
           echo “参数数量不对”
           echo $MSG
           return
   fi
   
   echo "模式" $1
   
   if [ "$1" != "Debug" -a "$1" != "Release" ]; then
           echo $MSG
           return
   fi
   
   # vulkan
   source vulkansdk/setup-env.sh
   
   # vsg
   export PATH=$(pwd)/install/$1/bin:$PATH
   export LD_LIBRARY_PATH=$(pwd)/install/$1/lib:$LD_LIBRARY_PATH
   
   echo PATH $PATH
   echo LD_LIBRARAY_PATH $LD_LIBRARY_PATH
   ```
