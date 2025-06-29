* 系统组件占用端口
  
  ```batch
  netsh int ip show excludedportrange protocol=tcp
  ```

* 查看应用端口占用
  
  ```batch
  netstat -ano | findstr "8080"
  ```

* 重启winnat服务
  
  可以解决一些port permission forbidden错误
  
  ```batch
  net stop winnat
  net start winnat
  ```
