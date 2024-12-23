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
