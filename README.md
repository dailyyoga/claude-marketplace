# dailyyoga Plugin Marketplace

Claude Code 插件和技能集合。

## 安装市场

```bash
# 从 GitHub 安装（推荐）
/plugin marketplace add https://github.com/<org>/dailyyoga-marketplace

# 或从本地路径安装
/plugin marketplace add ./dailyyoga-marketplace
```

## 可用插件

### git-commit-message

为 git diff 生成清晰的提交信息。适用于编写提交信息或审查暂存更改。

**安装:**

```bash
/plugin install git-commit-message@dailyyoga-marketplace
```

**使用:**

```bash
/git-commit-message
```

## 目录结构

```
dailyyoga-marketplace/
├── .claude-plugin/
│   └── marketplace.json    # 市场配置文件
├── git-commit-message/     # 提交信息生成插件
│   └── ...
└── README.md
```

## 开发新插件

1. 在 `dailyyoga-marketplace/` 下创建新的插件目录
2. 在 `.claude-plugin/marketplace.json` 中注册插件
3. 重新添加市场以更新插件列表

```json
{
  "plugins": [
    {
      "name": "your-plugin-name",
      "description": "插件描述",
      "source": "./your-plugin-dir"
    }
  ]
}
```
