# 安装

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
Invoke-RestMethod -Uri https://get.scoop.sh | Invoke-Expression
```

## 镜像

1. 移除默认国外仓库
   
   ```
   https://github.com/ScoopInstaller/Main.git 
   ```
   
   ```powershell
   scoop bucket rm main
   ```

2. 添加南京大学镜像
   
   ```powershell
   scoop bucket add main https://mirror.nju.edu.cn/git/scoop-main.git
   scoop bucket add extras https://mirror.nju.edu.cn/git/scoop-extras.git
   scoop update
   ```

# 常用命令

```powershell
list
search
info
depends
install
uninstall
update
```