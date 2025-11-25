# pip

## 通过文件列表安装包依赖

```bash
pip install -r requirements.txt
```

## 卸载所有第三方依赖包

```bash
pip freeze >a.txt
pip uninstall -r a.txt -y
```

## 更新软件包

```bash
pip install --upgrade name
```

# 全局变量

```python
s = "kdfjl" # 定义全局变量

# 使用
def fun():
    global s # 指明该变量是全局变量
    print(s)
```

# 异常

```python
try:
    print()
except IOError:
    print()
except Exception as e:
    print(str(e))
else:
    print()
```

# 字典

[Python 字典(Dictionary) | 菜鸟教程](https://www.runoob.com/python/python-dictionary.html)

* key的类型必须一致，value的类型任意

```python
d = {}
d = {"kdfj": 0, "dk" : 2}
d["dkfj"] = 5 # 增加或改值
d.pop("kdfj") # 删除
del d["kdfj"] # 删除
if "dk" in d: # 是否存在"dk"这个key
    pass
d.keys()    # 所有key
d.values()  # 所有value
```

# 列表

[Python 列表(List) | 菜鸟教程](https://www.runoob.com/python/python-lists.html)

* 元素类型任意

```python
l = []
l.append("dfkj") # 增加
l[2]  # 取值
l[-2] # 取值，倒数第二个
l[1:] # 截取

l.index("dfkj") # 搜索第一个匹配的下标
l.pop(index)    # 删除第index个元素，并返回值
l.remove("dfkj")# 删除第一个匹配的元素
```

# 字符串

[Python 字符串 | 菜鸟教程](https://www.runoob.com/python/python-strings.html)

* 前缀修饰符r
  
  ```python
  a = r"hello\nworld" # r表明这是一个普通字符串，忽略转义字符
  print(a) # 输出：hello\nworld
  ```

* 格式化输出
  
  ```python
  print "My name is %s and weight is %d kg!" % ('Zara', 21) 
  ```

* join 以分隔符串联字符串
  
  以 string 作为分隔符，将 seq 中所有的元素(的字符串表示)合并为一个新的字符串
  
  ```python
  C_FILE_SUFFIXES = ['hxx', 'cpp']
  '|'.join(C_FILE_SUFFIXES) # 'hxx|cpp'
  ```

# 执行外部命令

```python
import subprocess
p = subprocess.Popen(['ls', '-l'])
p = subprocess.Popen('ls', shell=True) # Windows可能需要shell才能执行，具体不明
p.wait() # 等待子进程结束

p = subprocess.Popen('ssh', stdin=subprocess.PIPE, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
out = p.stdout # 输出管道
out = p.stdin  # 输入管道
```
