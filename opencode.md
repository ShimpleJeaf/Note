# 插件配置

~/.config/opencode/opencode.json

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7-mcp": { // 文档获取
      "type": "local",
      "command": ["npx", "-y", "@upstash/context7-mcp"],
      "enabled": true
    }
  },
  "plugin": [
    "opencode-pty",
    "superpowers@git+https://github.com/obra/superpowers.git",
    "@tarquinen/opencode-dcp" // dcp（Dynamic Context Pruning）压缩
  ]
}
```

插件都是npm包，配置前先用npm install -g进行安装

# npm

**设置镜像**

```bash
# 淘宝
npm config set registry https://registry.npmmirror.com/
# 腾讯
npm config set registry https://mirrors.cloud.tencent.com/npm/
```

**设置代理**

```bash
npm config set proxy http://ip:port
npm config set https-proxy http://ip:port
```

# oh-my-opencode

不是很好用，暂时有些问题，不如自己手动配置插件

1. 安装
   
   ```bash
   npm install -g oh-my-opencode
   ```

2. 配置插件
   
   ```bash
   npx oh-my-opencode install
   ```

# codegraph

1. 安装
   
   ```cmd
   npm install -g @colbymchenry/codegraph
   ```

2. 配置opencode插件
   
   ```cmd
   codegraph install --target=opencode
   ```

3. 初始化代码图，项目独立
   
   ```bash
   cd project-dir
   codegraph init -i
   ```

# rtk

命令行输出裁剪工具

1. 安装rtk
   
   https://github.com/rtk-ai/rtk

2. 安装openrtk
   
   rtk opencode插件
   
   ```bash
   npm install -g openrtk
   ```

3. 配置openrtk
   
   ```json
   {
       "plugin": [openrtk]
   }
   ```

# browsermcp

https://github.com/browsermcp/mcp

1. 安装npm包
   
   ```bash
   npm install -g @browsermcp/mcp
   ```

2. 安装浏览器插件

3. 启动opencode，启用browsermcp

4. 启动浏览器，点击browsermcp插件上的connect，连接上后就可以用了
