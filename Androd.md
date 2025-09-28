# gradle

## 添加代理

项目目录下的gradle.properties文件中添加以下行

```properties
systemProp.http.proxyHost=127.0.0.1
systemProp.http.proxyPort=10808
systemProp.https.proxyHost=127.0.0.1
systemProp.https.proxyPort=10808
```

# Activity

一个Activity相当于一个界面，每个Activity可以调用其他Activity或其他应用的Activity

## 声明

Acivity在Manifest文件中声明

## 生命周期

各个生命周期阶段回调函数：

* onCreate()

* onStart()

* onResume()

* onPause()

* onStop()

* onRestart()

* onDestroy()
