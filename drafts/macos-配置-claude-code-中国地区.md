---
title: "MacOS 配置 Claude Code 中国地区"
date: 2025-12-29
category: "技术"
tags: ["Jetbrain", "Claude", "CC"]
draft: true
---

# MacOS 配置 Claude Code 中国地区

> 以 Jetbrain IDE 为例

## 准备工作

1. 在 Jetbrain IDE 中搜索插件 Claude Code、Claude Code GUI
2. Clash Party关闭系统代理，只打开虚拟网卡，切换到欧美地区（确保到时候能打开人机验证，比如我换到了美国），重启它

## 安装CC

1. 下载 Claude Code：https://claude.com/product/claude-code，命令行中执行：

    ```
    curl -fsSL https://claude.ai/install.sh | bash
    
    # 安装后添加到 PATH
    echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
    source ~/.zshrc
    
    # 验证安装
    claude --version
    ```
2. 编辑claude code文件：

    ```bash
    cp ~/.claude.json ~/.claude.json.backup
    
    code ~/.claude.json
    # 或
    open -a "TextEdit" ~/.claude.json
    ```

    在最后的 `}` 之前，找到 `"thinkingMigrationComplete": true` 这行，修改为：

    ```bash
      "thinkingMigrationComplete": true,
      "hasCompletedOnboarding": true
    }
    ```

    然后保存，再测试：

    ```bash
    # 保存后退出（Ctrl+X, Y, Enter）
    
    # 验证 JSON 格式是否正确（可选）
    python3 -c "import json; print(json.load(open('/Users/amada/.claude.json')))" > /dev/null && echo "JSON 格式正确" || echo "JSON 格式错误"
    
    # 再次尝试登录
    claude auth login
    ```


登陆成功，显示：

```bash
/login 
  ⎿  Login successful
```

![1767018564992-image-20251229221855467.png](/api/getImage?path=1767018564992-image-20251229221855467.png)
![1767018533234-image-20251229221915484.png](/api/getImage?path=1767018533234-image-20251229221915484.png)
![1767018546566-image-20251229222023697.png](/api/getImage?path=1767018546566-image-20251229222023697.png)