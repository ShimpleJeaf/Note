# 参数传递

%0 %1 %2

%0是命令，后面是参数

```batch
echo %1
```

* 获取参数数量
  
  ```batch
  set argC=0
  for %%x in (%*) do Set /A argC+=1
  echo %argC%
  ```

# 变量

等号两边不要留空格，好像有问题

```batch
set a=adfg
echo %a%
```

# 命令引用

```batch
set a=%cd%
```

# 时间戳

```batch
set timestr=%date:~0,4%%date:~5,2%%date:~8,2%%time:~0,2%%time:~3,2%%time:~6,2%
```


