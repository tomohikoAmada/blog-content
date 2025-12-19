---
title: "CLion 推送 GIthub"
date: 2025-12-19
category: "技术"
tags: ["Git"]
draft: false
---

# CLion 推送 GIthub

**1. 初始化Git仓库**

- 打开你的项目
- 点击顶部菜单 `VCS` → `Enable Version Control Integration`
- 选择 `Git`，点击OK

**2. 提交代码到本地仓库**

- 在Project视图中右键项目根目录
- 选择 `Git` → `Add` 添加文件到Git
- 点击顶部菜单 `Git` → `Commit`
- 写 commit message，点击`Commit`按钮

**3. 关联GitHub远程仓库**

如果右下角没有跳出 “share github” 之类的提示，这样做：

- 先在GitHub网站上创建一个新仓库（不要初始化README）
- 回到CLion，点击 `Git` → `Manage Remotes`
- 点击 `+` 添加远程仓库，Name填`origin`，URL填你GitHub仓库的地址
- 或者直接在Terminal执行：

```bash
git remote add origin https://github.com/你的用户名/仓库名.git
```

**4. 推送到GitHub**

- 点击 `Git` → `Push`
- 第一次推送选择 `Define remote`，然后Push

**5. 之后更新代码到GitHub**

- 修改代码后，按快捷键 **`Cmd + K`**
- 在弹出的 Commit 窗口中：
  - 勾选要提交的文件（通常全选）
  - 在下方输入 commit message（描述你改了什么）
  - 点击 **"提交并推送"**（Commit and Push）按钮
- 搞定！