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

# 异常

```python
try:
    print()
except IOError:
    print()
else:
    print()
```
