# 设置代理

在CMakelist.txt中添加

```cmake
set(ENV{http_proxy} "http://127.0.0.1:8082")
set(ENV{https_proxy} "http://127.0.0.1:8082")
```

或者直接在环境变量中添加这两个变量


