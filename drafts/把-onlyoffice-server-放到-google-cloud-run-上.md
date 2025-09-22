---
title: "把 onlyoffice-server 放到 Google Cloud Run 上"
date: 2025-09-22
category: "技术"
tags: ["Google Cloud", "Serverless", "ONLYOFFICE"]
draft: true
---

# 把 onlyoffice-server 放到 Google Cloud Run 上

## 部署步骤

填写服务名称：onlyoffice-server
容器镜像网址：onlyoffice/documentserver
区域：asia-east2 (香港)
第2步：配置高级设置
点击 "容器、卷、网络、安全性" 展开高级配置：
容器设置：

端口：80
内存：选择 4GiB 或 8GiB（OnlyOffice需要较多内存）
CPU：选择 2 或 4

环境变量：
点击"添加环境变量"，添加：
名称：JWT\_ENABLED
值：true

名称：JWT\_SECRET
值：your-secret-key-here
第3步：存储配置（重要）
由于OnlyOffice需要持久存储，您需要：

先创建Cloud Storage存储桶：

在Google Cloud控制台搜索"Cloud Storage"
点击"创建存储桶"
存储桶名称：onlyoffice-data-bucket
位置：选择"asia-east2"
点击创建

配置卷挂载：

在Cloud Run配置中点击"卷"标签
添加卷：

```
 名称：onlyoffice-storage
 类型：Cloud Storage
 存储桶名称：onlyoffice-data-bucket
 挂载路径：/var/www/onlyoffice/Data
```

第4步：部署命令方式（推荐）
如果界面配置复杂，可以使用命令行：
bash# 1. 创建存储桶
gsutil mb gs://onlyoffice-data-your-project-id

# 2\. 部署服务

gcloud run deploy onlyoffice-server --image=onlyoffice/documentserver --region=asia-east2 --memory=8Gi --cpu=4 --port=80 --set-env-vars="JWT\_ENABLED=true,JWT\_SECRET=your-secret-key" --execution-environment=gen2 --add-volume=name=onlyoffice-data,type=cloud-storage,bucket=onlyoffice-data-your-project-id --add-volume-mount=volume=onlyoffice-data,mount-path=/var/www/onlyoffice/Data --allow-unauthenticated --max-instances=5 --min-instances=0
第5步：验证部署

部署完成后，Google会给您一个网址
访问该网址，如果看到OnlyOffice欢迎页面，说明成功
网址格式类似：https://onlyoffice-server-xxx-xxx.a.run.app

重要注意事项
成本考虑

OnlyOffice内存需求大（建议8GB），Cloud Run按使用计费
8GB内存 + 4CPU的配置成本相对较高
建议设置预算警报

性能限制

Cloud Run是无状态的，OnlyOffice可能会有性能问题
如果遇到问题，建议考虑使用Compute Engine

替代建议
如果Cloud Run部署遇到困难，建议使用Compute Engine：
bash# 创建虚拟机
gcloud compute instances create onlyoffice-vm --zone=asia-east2-a --machine-type=e2-standard-4 --image-family=cos-stable --image-project=cos-cloud --boot-disk-size=50GB

# SSH连接后运行

docker run -d -p 80:80 -v /opt/onlyoffice/DocumentServer/logs:/var/log/onlyoffice -v /opt/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data -e JWT\_SECRET=your-secret-key onlyoffice/documentserver