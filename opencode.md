# 插件配置

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "context7": {
      "command": ["npx", "-y", "@upstash/context7-mcp"],
      "enabled": true,
      "type": "local"
    },
    "fetch": {
      "command": ["uvx", "mcp-server-fetch"],
      "enabled": true,
      "type": "local"
    },
    "memory": {
      "command": ["npx", "-y", "@modelcontextprotocol/server-memory"],
      "enabled": true,
      "type": "local"
    }
  },
  "plugin": [
    "@nick-vi/opencode-type-inject",
    "opencode-supermemory",
    "opencode-browser",
    "@mohak34/opencode-notifier",
    "@tarquinen/opencode-dcp"
  ]
}
```

# codegraph

1. 安装
   
   通过npm安装
   
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
