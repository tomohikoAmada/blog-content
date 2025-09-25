---
title: "Langchain-Chatchat Deploy"
date: 2025-09-25
category: "技术"
tags: []
draft: true
---

# Langchain-Chatchat Deploy

打开pycharm，配置虚拟环境，首先运行 `pip install langchain-chatchat` 如下：
![1758804593722-image.png](/api/getImage?path=1758804593722-image.png)
然后执行 `chatchat init`如下：
![1758804711120-image.png](/api/getImage?path=1758804711120-image.png)
然后执行 `chatchat kb -r`
然后把 `model_settings.yaml`里的换掉，执行：

```
sed -i '' 's/DEFAULT_LLM_MODEL: text-embedding-3-small/DEFAULT_LLM_MODEL: gpt-3.5-turbo/' model_settings.yaml
```

```
sed -i '' 's/DEFAULT_EMBEDDING_MODEL: bge-m3/DEFAULT_EMBEDDING_MODEL: text-embedding-3-small/' model_settings.yaml
```

然后执行 `chatchat kb -r`