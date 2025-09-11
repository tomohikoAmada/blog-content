
![image-20250911141315304](https://raw.githubusercontent.com/tomohikoAmada/blog-content/main/images/image-20250911154750327.png)
## **Vue + Firebase + GitHub 现代博客系统**

## 📋 **系统概述**

### **核心理念**

- **Git作为数据库**：利用GitHub仓库存储Markdown文章，享受版本控制、协作编辑等Git天然优势
- **三级缓存策略**：localStorage → Firebase CDN → 动态生成
- **完全无服务器**：零运维成本，自动扩展
- **内容与代码分离**：文章内容存储在GitHub，应用代码部署到Firebase

------

## 🏗️ **技术架构**

### **技术栈组成**

```
前端层：Vue 3 + Vite
编辑层：Vditor
渲染层：markdown-it
服务层：Firebase Functions
存储层：GitHub仓库
缓存层：localStorage + Firebase CDN
部署层：Firebase Hosting 托管
```

### **数据流架构**

#### **内容创作流程：**

```
作者编写 → Vditor编辑器 → 实时预览 → Firebase Functions → GitHub API → 提交到仓库
         (在线编辑md)                  (权限验证)        (版本控制)
```

#### **内容展示流程：**

```
用户请求文章
    ↓
localStorage检查 (TTL: 1小时)
    ├─ 命中 → 直接返回 (毫秒级)
    └─ 未命中 
        ↓
Firebase CDN检查 (TTL: 2小时)  
    ├─ 命中 → 返回 + 写入localStorage
    └─ 未命中
        ↓
Firebase Functions调用
    ↓
GitHub API获取最新内容
    ↓
同时写入Firebase CDN + localStorage
    ↓
返回给用户
```

Firebase Hosting 自带 CDN

------

## 💾 **存储架构设计**

### **GitHub仓库结构**

```
blog-content/
├── posts/                 # 已发布文章
│   ├── 2025/
│   │   ├── tech/
│   │   │   ├── vue-blog-system.md
│   │   │   └── firebase-optimization.md  
│   │   └── life/
│   └── 2024/
├── drafts/                # 草稿箱
├── assets/                # 静态资源（图片、视频）
├── metadata/              # 文章元数据和索引
└── templates/             # 文章模板
```

### **缓存策略设计**

```
┌─────────────┐    ┌──────────────┐    ┌─────────────┐
│ localStorage │◄──►│ Firebase CDN │◄──►│   GitHub    │
│   1小时TTL   │    │   2小时TTL    │    │  永久存储   │
│   客户端     │    │   全球节点    │    │  版本控制   │
└─────────────┘    └──────────────┘    └─────────────┘
     ↑95%命中           ↑4%命中            ↑1%调用
   毫秒级响应         100-200ms        500-1000ms
```





## Vue

### 文件结构

```
blog/                              # 项目根目录
├── src/                           # Vue 前端项目
│   ├── main.js                    # 应用入口
│   ├── App.vue                    # 根组件
│   │
│   ├── views/                     # 页面组件
│   │   ├── HomeView.vue          # 首页 (列表 + 搜索 + 分页)
│   │   └── PostView.vue          # 文章详情页
│   │
│   ├── components/                # 组件
│   │   ├── AppHeader.vue         # 导航栏
│   │   ├── PostCard.vue          # 文章卡片
│   │   └── Loading.vue           # 加载组件
│   │
│   ├── services/                  # 服务
│   │   ├── blogService.js        # 博客API
│   │   └── markdown.js           # Markdown处理
│   │
│   ├── utils/                     # 工具
│   │   └── helpers.js            # 辅助函数
│   │
│   └── assets/                    # 静态资源
│       ├── style.css             # 全局样式
│       └── images/               # 网站图片
│           └── logo.png
│
├── functions/                     # Firebase Functions (已有)
├── package.json                  # 依赖
├── vite.config.js                # 配置
└── README.md                     # 说明
```

```
blog-content/                     # GitHub 仓库
├── posts/                       # 文章
│   ├── hello-world.md
│   ├── vue-tutorial.md
│   └── firebase-blog.md
│
├── images/                      # 图片 (扁平结构)
│   ├── hello-world-cover.jpg
│   ├── vue-tutorial-demo.png
│   ├── firebase-architecture.svg
│   └── avatar.jpg
│
└── README.md
```



https://raw.githubusercontent.com/tomohikoAmada/blog-content/main/images/xxx.png
