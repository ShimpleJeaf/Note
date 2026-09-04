# 编译

1. 下载依赖：

   ```bash
   sudo apt install libvulkan-dev libxrandr-dev libwayland-dev wayland-protocols
   ```

2. 编译vulkan-sdk

   * vulkan-sdk不是单一的仓库，是一系列vulkan工具的集合，包含vulkan-headers、spirv-headers、vulkan-tools、shaderc、glslang等，所有的组件及对应的版本可在vulkan-sdk的release-notes中查看

     [1.4.350.0/linux/release_notes](https://vulkan.lunarg.com/doc/sdk/1.4.350.0/linux/release_notes.html)

   * 统一编译脚本说明：

     [Helper build script](https://vulkan.lunarg.com/doc/view/latest/linux/getting_started.html#helper-build-script)

   * 使用vulkaninfo --summary查看gpu支持的apiVersion，下载sdk时不要太新，loader inferface可能不兼容

   * 下载官方的vulkan-sdk预编译包，里面有编译脚本vulkansdk，这是一个sh脚本

     [vulkan-sdk download](https://vulkan.lunarg.com/sdk/home)

     下载linux版Tarball包并解压，看里面的预编译文件是否可用，一般是不可用的，需要重新编译
     ```bash
     cd vulkansdk/x86_64/bin
     ./vkcubeapp
     ```
   
   * 编译

     删除里面的预编译文件：x86_64文件夹，新编译的文件也会放在这里面

     Tarball包里是没有源码的，脚本会自动克隆相应的仓库，所以这一步需要联网

     如果不能联网，可以提取脚本中的仓库clone操作，提前下载，然后修改脚本，跳过克隆，直接编译

     脚本还会自动下载依赖包，查看脚本的install_deps()函数可以知道需要哪些依赖包

     运行编译脚本：

     ```bash
     ./vulkansdk -j 16 glslang vulkan-headers loader tools shaderc spirv-headers spirv-tools
     ```