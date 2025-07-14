# ssh

1. 安装ssh服务
   
   ```bash
   pkg install openssh
   ```

2. 开启服务
   
   ```bash
   sshd
   ```

3. 在termux上查看ip和用户名
   
   ```bash
   ifconfig 
   whoami 
   # u0_a167
   ```

4. 设置账户密码
   
   ```bash
   passwd
   ```

5. 连接
   
   termux上ssh服务的端口是8022

# 中文输入法

如果右上角没有键盘图标，说明ibus服务异常退出了，在终端执行

```bash
ibus-daemon -drx
```

# WindTerm

如果遇到ssh认证闪退

新建会话 > SSH > 验证 > 取消“尝试公钥认证”、“尝试键盘交互认证”、”尝试gssapi-with-mic身份验证“
