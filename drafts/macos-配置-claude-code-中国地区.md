---
title: "MacOS 配置 Claude Code 中国地区"
date: 2025-12-29
category: "技术"
tags: ["ClaudeCode"]
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

<br>
    > 如有失败，尝试清除指纹：
    >
    > ```bash
    > # 1. 清除 Claude Code 的配置和缓存
    > rm -rf ~/.claude/statsig
    > rm -rf ~/.claude/.claude-session-*
    > 
    > # 2. 清除浏览器 Cookie（在浏览器中操作）
    > # 访问 chrome://settings/siteData
    > # 搜索 anthropic.com 并删除所有数据
    > 
    > # 3. 重启 Clash Party（完全退出后重新打开）
    > 
    > # 4. 重启终端
    > 
    > # 5. 再次尝试
    > claude auth login
    > ```
    >
    > 再重启一切：
    >
    > ```bash
    > # 1. 完全退出 Clash Party
    > # 2. 重新打开 Clash Party
    > # 3. 确认连接到美国/欧洲节点
    > # 4. 重启终端（完全关闭再打开）
    > ```
    >
    > 可以尝试强制 OAuth 模式。有些用户报告通过特定的浏览器设置成功，即尝试使用浏览器模式绕过：
    >
    > ```bash
    > # 设置环境变量强制使用特定认证流程
    > export ANTHROPIC_AUTH_TYPE="oauth"
    > 
    > # 启动
    > claude auth login
    > ```
    >
    > 测试网络连接：
    >
    > ```bash
    > # 应该返回 200 或 Cloudflare 验证页面
    > curl -I https://console.anthropic.com
    > 
    > # 检查 cf-ray 显示的地区代码（应该是美国/欧洲）
    > ```

登陆成功，显示：

```bash
/login 
  ⎿  Login successful
```

![1767018564992-image-20251229221855467.png](/api/getImage?path=1767018564992-image-20251229221855467.png)
![1767018533234-image-20251229221915484.png](/api/getImage?path=1767018533234-image-20251229221915484.png)
![1767018546566-image-20251229222023697.png](/api/getImage?path=1767018546566-image-20251229222023697.png)