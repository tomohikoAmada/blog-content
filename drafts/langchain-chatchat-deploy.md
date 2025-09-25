---
title: "Langchain-Chatchat Deploy"
date: 2025-09-25
category: "技术"
tags: []
draft: true
---

# Langchain-Chatchat Deploy

打开pycharm，配置虚拟环境，确保是 **python@3.11**，首先运行 `pip install langchain-chatchat` 如下：
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

然后修改 `model_settings.yaml `的 `platform_name: openai`，把`api_key`改成自己的，

> 如果用的是别的平台，eg：
>
> ### 1. **使用阿里云百炼平台**
>
> 修改 `model_settings.yaml`：
>
> ```yaml
> # 默认选用的 LLM 名称
> DEFAULT_LLM_MODEL: qwen-turbo
> 
> # 默认选用的 Embedding 名称  
> DEFAULT_EMBEDDING_MODEL: text-embedding-v1
> 
> MODEL_PLATFORMS:
>   - platform_name: dashscope
>     platform_type: openai  # 使用 OpenAI 兼容接口
>     api_base_url: https://dashscope.aliyuncs.com/compatible-mode/v1
>     api_key: sk-your-dashscope-api-key  # 阿里云百炼的 API Key
>     api_proxy: ''
>     api_concurrencies: 5
>     auto_detect_model: false
>     llm_models:
>       - qwen-turbo
>       - qwen-plus  
>       - qwen-max
>       - qwen2-72b-instruct
>     embed_models:
>       - text-embedding-v1
>       - text-embedding-v2
> ```
>
> ### 2. **使用通义千问开放平台**
>
> ```yaml
> MODEL_PLATFORMS:
>   - platform_name: qwen
>     platform_type: openai
>     api_base_url: https://dashscope.aliyuncs.com/compatible-mode/v1
>     api_key: your-dashscope-api-key
>     api_proxy: ''
>     api_concurrencies: 5
>     auto_detect_model: false
>     llm_models:
>       - qwen-turbo
>       - qwen-plus
>       - qwen-max
>       - qwen-long
>     embed_models:
>       - text-embedding-v1
> ```

然后执行 `chatchat kb -r`
然后让这个库退一个版本，执行`pip install httpx==0.24.1`
然后运行 `chatchat start -a`
即可看到界面
![1758804947467-image.png](/api/getImage?path=1758804947467-image.png)