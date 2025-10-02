---
title: "Deploying RSSHub on Google Cloud Run"
date: 2025-10-02
category: "技术"
tags: ["RSSHub"]
draft: true
---

# Deploying RSSHub on Google Cloud Run

**RSSHub 官方文档主要提供了 Google App Engine (GAE) 的部署方法**，但实际上将 RSSHub 部署到 Google Cloud Run 是完全可行的，而且可能更灵活、更经济。以下是详细的部署步骤：

1. 创建 GCP 项目并启用计费
2. 安装工具：
    * [Google Cloud CLI (gcloud)](https://cloud.google.com/sdk/docs/install)
    * Docker（可选，如果本地构建）

    > RSSHub 提供了 Docker 镜像，支持两种主要版本：`diygod/rsshub:latest`（不含 Puppeteer）和 `diygod/rsshub:chromium-bundled`（包含 Chromium，支持更多路由但消耗更多资源）
3. 本地切换过来，执行：`gcloud config set project news-473903`
    查看当前配置：`gcloud config list`
4. 启用必要的 API：

    ```bash
    # 启用 Cloud Run API
    gcloud services enable run.googleapis.com
    # 启用 Artifact Registry API（用于存储容器镜像）
    gcloud services enable artifactregistry.googleapis.com
    ```
5. 部署：

    ```bash
    gcloud run deploy rsshub \
      --image=diygod/rsshub:chromium-bundled \
      --platform=managed \
      --region=asia-northeast2 \
      --allow-unauthenticated \
      --port=1200 \
      --memory=2Gi \
      --cpu=2 \
      --timeout=300 \
      --concurrency=10 \
      --max-instances=5 \
      --set-env-vars="NODE_ENV=production,CACHE_EXPIRE=3600,LOGGER_LEVEL=info"
    ```


[查看具体对话](https://claude.ai/share/41eec45b-f782-40a2-9852-4322a70cb13d)