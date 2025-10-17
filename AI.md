# 基本概念

* NLP 
  
  Natual Language Processing
  
  自然语言处理

* LLM 
  
  Large Language Model
  
  大语言模型
  
  用于自然语言处理

* Embedder Model
  
  Embedder：嵌入
  
  通过将文本转换为向量表示，这些模型帮助计算机理解和处理自然语言

* Speech model
  
  语音模型

* Vector Database
  
  向量数据库，是一种专门用于存储和处理向量数据的数据库系统

# pip

* 卸载所有第三方依赖包
  
  pip freeze >a.txt
  
  pip uninstall -r a.txt -y

# cuda tensorrt onnx

* pip install -r requirement.txt更新包依赖
  
  查看requirement.txt中对onnx-runtime的版本要求

* **onnxruntime对cuda、cuDNN、tensorrt的版本要求**
  
  [NVIDIA - CUDA | onnxruntime](https://onnxruntime.ai/docs/execution-providers/CUDA-ExecutionProvider.html)
  
  * 注意：对pytorch的版本也有要求
    
    Not available in PyPI. See [Install ORT](https://onnxruntime.ai/docs/install) for details. Compatible with PyTorch <= 2.3.1 for CUDA 11.8.

* 下载对应的onnxruntime
  
  注意：cuda对应的是onnxruntime-gpu，这个和onnxruntime的版本号是一样的，onnxruntime应该是cpu版本
  
  [Install ONNX Runtime | onnxruntime](https://onnxruntime.ai/docs/install/)

* torch torchvision torchaudio
  
  * [Start Locally | PyTorch](https://pytorch.org/get-started/locally/)
    
    进去选对应的平台，就会生成对应的pip install命令，执行就行了
  
  * pytorch下载链接
    
    [download.pytorch.org/whl/torch](https://download.pytorch.org/whl/torch)
    
    pip install 安装包名
  
  **注意**：要先卸载旧的版本，不然新版本安装不上，而且安装过程不会报错，很容易注意不到。安装完后用pip list查看安装后的版本是否正确

* cuda下载链接
  
  nvidia-smi命令查看显卡支持的最高cuda版本
  
  [CUDA Toolkit Archive | NVIDIA Developer](https://developer.nvidia.com/cuda-toolkit-archive)
  
  测试cuda是否安装成功：nvcc -V

* cudnn下载链接
  
  [cuDNN Archive | NVIDIA Developer](https://developer.nvidia.com/cudnn-archive)
  
  下载完成后把文件解压到cuda的安装目录
  
  9.0以上版本只有exe了

* tensorrt下载链接
  
  [TensorRT Download | NVIDIA Developer](https://developer.nvidia.com/tensorrt/download)
  
  把下载的文件夹解压到一个目录，并将lib目录加入到PATH环境变量中
  
  转换成的tensor模型在facefusion/.caches/目录下

* 判断是否安装成功
  
  ```python
  import onnxruntime
  print(onnxruntime.__version__)
  print(onnxruntime.get_device() ) # 如果得到的输出结果是GPU，所以按理说是找到了GPU的
  ort_session = onnxruntime.InferenceSession("your_onnx_module_path.onnx", 
      providers=['CUDAExecutionProvider']) # TensorrtExecutionProvider
  print(ort_session.get_providers()) # 回打印实际使用的设备
  ```

* 注意，conda虚拟环境可以单独设置环境变量
  
  添加了系统环境变量后一定要看conda虚拟环境下的环境变量是否正确
  
  列出环境变量
  
  ```powershell
  conda env config vars list
  ```
  
  如果只想改变 “某个虚拟环境” 的环境变量可通过 `-n` 参数
  
  ```powershell
  conda env config vars set PATH=/mypath/cuda11.06/bin:$PATH -n my_env
  conda env config vars set LD_LIBRARY_PATH=/mypath/cuda11.06/lib64:$LD_LIBRARY_PATH -n my_env
  conda env config vars set CUDA_HOME=/mypath/cuda -n my_env
  ```
  
  也可以改所有环境的环境变量
  
  ```powershell
  conda env config vars set PATH=/mypath/cuda11.06/bin:$PATH
  conda env config vars set LD_LIBRARY_PATH=/mypath/cuda11.06/lib64:$LD_LIBRARY_PATH
  conda env config vars set CUDA_HOME=/mypath/cuda
  ```
  
  删除环境变量
  
  ```powershell
  conda env config vars unset PATH
  ```

# facefusion

# Hugging Face

镜像站：

HF-Mirror  https://hf-mirror.com/

在open-webui中使用：

* Windows
  
  set HF_ENDPOINT=https://hf-mirror.com

* Linux
  
  export HF_ENDPOINT=https://hf-mirror.com 

# Anything LLM

# Ollama + open-webui

## 1. Ollama

https://ollama.com

* 下载并安装
  
  指定安装目录：
  
  ```bash
  OllamaSetup.exe /DIR=D:\Software\Ollama
  ```

* 在官网搜索需要的模型，模型内容里有下载的命令行
  
  如：
  
  ```batch
  ollama run deepseek-r1:8b
  ```
  
  会下载并运行该模型，后面的8b表示模型参数数量

* ollama的默认端口号为11434

* 命令行启动服务，方便看日志
  
  ```batch
  ollama serve
  ```

* 允许外网访问
  
  如果需要限制端口号，改为0.0.0.0:port即可
  
  * Linux
    
    ```shell
    # 停止服务
    systemctl stop ollama
    vim /etc/systemd/system/ollama.service
    # 增加两行
    Environment="OLLAMA_HOST=0.0.0.0"
    Environment="OLLAMA_ORIGINS=*"
    # 重载daemon文件 
    systemctl daemon-reload
    # 启动服务
    systemctl start ollama
    ```
  
  * Windows
    
    ```batch
    :: 添加两个环境变量
    OLLAMA_HOST=0.0.0.0
    OLLAMA_ORIGINS=*
    ```

* 更改模型存放位置
  
  ```batch
  :: 添加环境变量
  OLLAMA_MODELS=D:\software\ollama\models
  ```

* 刷新环境变量
  
  win+R进入cmd
  
  echo %path%
  
  即可

* Visual Studio插件
  
  LLM Copilot

## 2 open-webui

## 2.1 安装

使用pip安装

* 下载安装python11，如果有多个python版本，就不要加入到环境变量path中

* 用python11建一个虚拟环境
  
  ```batch
  (your driver path)\python11\python.exe -m venv venv
  ```

* 激活虚拟环境
  
  ```batch
  call venv/scripts/activate.bat
  ```

* 安装open-webui
  
  ```batch
  pip install open-webui
  ```

* 启动脚本
  
  ```batch
  call venv/scripts/activate.bat
  :: 设置Hugging Face的镜像站
  set HF_ENDPOINT=https://hf-mirror.com
  :: 启动服务
  open-webui serve
  ```

* open-webui的默认网页地址是0.0.0.0:3000，可能无法访问可以换成localhost:3000

### 2.2 使用函数

官网：[Open WebUI](https://openwebui.com/)

在官网主页可以查找下载模型、函数、提示词、工具。

* 限制输入Context Length
  
  deepseek模型默认的num_ctx比较小，且不支持k-shift自动滑窗，输入context长度过长模型就会崩溃，可以用以下函数进行限制。open-webui输入的默认长度非常长，具体是多少不知道，最少应该有20个对话历史。
  
  函数地址：[Context Length Filter (tokens & turns) Function | Open WebUI Community](https://openwebui.com/f/keke/context_length_filter)
  
  下载后在函数页面导入，全局开启，或者在主页的工作空间中新建一个模型，在模型中加入该函数过滤器。
