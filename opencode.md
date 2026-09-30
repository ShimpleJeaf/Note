# 快捷键

[快捷键 | OpenCode](https://opencode.ai/docs/keybinds)

| 功能                  | 快捷键       |
| --------------------- | ------------ |
| 换行                  | Ctrl + Enter |
| 切换model variant变体 | Ctrl + T     |

# 模型配置

通常用opencode自带的就可以了，如果想自定义，最好直接重定义一个provider，名字要和自带的区分。然后在自定义的provider下定义自己的模型和模型参数。

如果自定义的provider和自带的名字一样，/connect的时候将看到两个同名的provider，没法区分。虽然这两个provider内的model是分开的，但是使用的api-key却是共用的，只用配置一个的api-key。

不过，如果provider的名字不一样，则需要配置baseURL

不要试图直接修改opencode自带的model。

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {
    "MyDeepSeek": {
      "options": {
        "baseURL": "https://api.deepseek.com"
      },
      "models": {
        "deepseek-v4-flash": { // 名字必须和提供商给的一致
          "variants": {
            "fast": {
              "thinking": { "type": "disabled" }
              //"reasoning_effort": "low"
            }
          }
        }
      }
    }
  }
｝
```

如果不需要变体variants，把variants整个删掉就行了

```json
"models": {
  "deepseek-v4-flash": {}
}
```

# 权限配置

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
	"edit": {
	  "/mnt/c/Projects/testProj/docs/**": "allow"
	}
  }
}
```

# 插件配置

~/.config/opencode/opencode.jsonc

```json
{
  "$schema": "https://opencode.ai/config.json",
  "lsp": false, // 关闭语言服务器
  "compaction": {
    "auto": false // 关闭自动压缩
  },
  "mcp": {
    "browsermcp": {
      "type": "local",
      "command": ["npx", "-y", "@browsermcp/mcp"],
      "enabled": false
    },
    "context7-mcp": { // 文档获取
      "command": ["npx", "-y", "@upstash/context7-mcp"],
      "enabled": true,
      "type": "local"
    },
    "codebase-memory-mcp": {
      "command":["/home/shimple/.local/bin/codebase-memory-mcp"],
      "type":"local"
    }
  },
  "plugin": [
    "openrtk",
    "superpowers@git+https://github.com/obra/superpowers.git",
    "opencode-pty",
    "@tarquinen/opencode-dcp",
    "opencode-visual-cache", // 缓存命中情况，还能显示上下文各类型的占比
    "/home/shimple/.config/opencode/vendor/i-have-adhd/.opencode/plugins/i-have-adhd.mjs"
  ]
}
```

opencode-visual-cache还需要配置tui

~/.config/opencode/tui.jsonc

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "plugin": [
    "opencode-visual-cache@latest" 
  ]
}
```

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

# ~~oh-my-opencode~~

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

   查看codegraph官网安装说明

   [colbymchenry/codegraph: Get Started](https://github.com/colbymchenry/codegraph#get-started)

   ```bash
   # macOS / Linux
   curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh
   
   # Windows (PowerShell)
   irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex
   ```

2. 配置opencode插件

   ```cmd
   codegraph install
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

5. 额外配置忽略和包含

   优先级高于git repo和.gitignore

   codegraph.json放在项目根目录

   ```json
   {
     "exclude": ["static/", "**/vendor/**"],
     "include": ["Tools/", "Local/typescript/"]
   }
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

2. 安装**浏览器插件**

3. 启动opencode，启用browsermcp

4. 启动浏览器，点击browsermcp插件上的connect，连接上后就可以用了

# codebase-memory-mcp

https://github.com/DeusData/codebase-memory-mcp

高性能Code Agent MCP服务器，将代码库索引为持久化的知识图谱

* 安装

  ```bash
  curl -fsSL https://raw.githubusercontent.com/DeusData/codebase-memory-mcp/main/install.sh | bash
  ```

* 在opencode session内，当涉及代码查询时，会自动索引整个项目，不需要额外配置。

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

# ~~graphify~~

Turn any codebase, with its docs, SQL schemas, configs, and PDFs, into a queryable knowledge graph

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

# opencode-dynamic-context-pruning (dcp)

* github地址
  [Opencode-DCP/opencode-dynamic-context-pruning](https://github.com/Opencode-DCP/opencode-dynamic-context-pruning)

* 配置

  opencode.json

  ```json
  {
    "plugin": [ "@tarquinen/opencode-dcp" ]
  }
  ```

* 配置文件：.config/opencode/dcp.json

  默认配置文件在仓库的README.md中

* 改为手动模式：

  ```json
  {
      "manualMode": {
          "enabled": true,
          // When true, automatic cleanup (deduplication, purgeErrors)
          // still runs even in manual mode
          "automaticStrategies": true,
      }
  }
  ```

# opencode-system-prompt-logger

查看系统提示词的工具

* 日志位置

  ```bash
  opencode debug paths
  ```

* 配置 

  opencode.json

  ```json
  {
      "plugin": ["opencode-system-prompt-logger"]
  }
  ```
