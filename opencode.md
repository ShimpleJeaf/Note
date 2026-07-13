# 换行

ctrl + enter

# 插件配置

~/.config/opencode/opencode.json

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": false,
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

4. 其他操作
   
   ```bash
   # 创建索引，如果init的时候没加-i的话，后续可以调用index进行
   codegraph index
   
   # 同步索引
   codegraph sync
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

4. 测试
   
   在opencode中输入ll，如果输出rtk ls -la说明安装成功

# browsermcp

https://github.com/browsermcp/mcp

1. 安装npm包
   
   ```bash
   npm install -g @browsermcp/mcp
   ```

2. 安装浏览器插件

3. 启动opencode，启用browsermcp

4. 启动浏览器，点击browsermcp插件上的connect，连接上后就可以用了

# claude-mem

1. 安装
   
   ```bash
   npx claude-mem install --ide opencode
   ```

2. 在opencode.json的plugin里添加claude-mem

3. 在opencode之前启动claude-mem
   
   ```bash
   npx claude-mem start
   ```

4. 自动启动，在.bashrc中添加命令别名
   
   ```bash
   alias opencode='npx claude-mem start && opencode'
   ```

# context7-mcp

Context7 是一个基于 Model Context Protocol（MCP）的“文档拉取”服务。它能在你给 LLM（如 Claude、Cursor Copilot、Windsurf 等）的提示（prompt）中，动态地注入**从源头获取的、与你所用库版本完全对应**的官方文档和代码示例，从而避免：

- 过时的示例、基于老旧训练数据的答案
- LLM 幻觉出不存在的 API
- 通用但与当前版本不符的代码片段

使用方法：

在正常的输入后面加上use context7

例如：

```bash
告诉我qwidget怎么用。use context7。
```

# graphify

* 安装
  
  ```bash
  uv tool install graphifyy
  graphify install --platform opencode
  ```

* /graphify命令
  
  /graphify是opencode内的命令，但是不能自动补全，因为他的形式是skill，不支持自动补全
  
  ```bash
  # 初始化
  /graphify .
  # 添加知识
  /graphify add https://arxiv.org/abs/1706.03762
  ```

* 额外ignore
  
  .graphifyignore文件
  
  ```gitignore
  dist/
  !/install/include
  ```
