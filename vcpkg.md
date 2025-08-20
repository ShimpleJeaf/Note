# 安装

1. 克隆vcpkgbash
   
   ```bash
   git clone https://github.com/microsoft/vcpkg.git
   ```

2. 安装
   
   **Windows**
   
   ```bash
   .\bootstrap-vcpkg.bat
   ```
   
   **Linux/macOS**
   
   ```bash
   ./bootstrap-vcpkg.sh
   ```
   
   可将vcpkg命令目录加入环境变量path

3. 设置默认架构
   
   在环境变量中加入VCPKG_DEFAULT_TRIPLET
   
   ```bash
   # linux
   export VCPKG_DEFAULT_TRIPLET=x64-windows
   ```

# 代理

使用环境变量设置代理

* http代理
  
  ```bash
  set http_proxy=http://127.0.0.1:10808
  set https_proxy=http://127.0.0.1:10808
  ```

* socks5代理
  
  ```bash
  set http_proxy=socks5://127.0.0.1:10808
  set https_proxy=socks5://127.0.0.1:10808
  ```

# 命令

* 查看依赖
  
  ```bash
  vcpkg depend-info <software>
  ```

* 安装
  
  ```bash
  vcpkg install <software>
  # 指定架构
  vcpkg install <software>[:triplet]
  ```

* 搜索
  
  ```bash
  vcpkg search <software>
  ```

* 列出安装的包
  
  ```bash
  vcpkg list
  ```

* 使用vcpkg.json指定项目依赖
  
  * vcpkg.json放在项目文件下，示例：
    
    ```json
    {
        "$schema":  "https://raw.githubusercontent.com/microsoft/vcpkg-tool/main/docs/vcpkg.schema.json",
        "dependencies": [
            "glew",
            "glfw3",
            "spdlog",
            "glm"
        ]
    }
    ```
  
  * 安装依赖
    
    在vcpkg.json目录下运行以下命令
    
    ```bash
    vcpkg install 
    # 指定架构安装
    vcpkg install --triplet x86-windows
    ```

# 集成

* 集成使用
  
  ```bash
  vcpkg integrate install
  ```

* 集成移除
  
  ```bash
  vcpkg integrate remove
  ```
