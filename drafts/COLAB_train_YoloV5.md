---
title: "COLAB YOLOV5 训练 调优"
date: 2025-09-15
category: "技术"
tags: ["YOLOV5", "training", "GCP", "COLAB"]
draft: true
---

# COLAB 训练 调优 YOLOV5

## 本地测试

新建 dataset 文件夹和 data.yaml，如下：

```
yolov5/
└── dataset/
    ├── data.yaml
    ├── train/
    │   ├── images/
    │   │   └── (训练图片)
    │   └── labels/
    │       └── (对应的.txt标注文件)
    ├── val/
    │   ├── images/
    │   │   └── (验证图片)
    │   └── labels/
    │       └── (对应的.txt标注文件)
    └── test/
        ├── images/
        │   └── (测试图片)
        └── labels/
            └── (对应的.txt标注文件)
```

其中，data.yaml：

```
path: dataset  # 数据集根目录
train: train/images  # 训练集图片相对路径
val: val/images      # 验证集图片相对路径
test: test/images    # 测试集图片相对路径


nc: 6   # 类别数量
names: ['Crazing', 'Inclusion', 'Patches', 'Pitted Surface', 'Rolled-in Scale', 'Scratches']  # 类别名称
```

标注文件（.txt）里的第一个数字（0）是类别的索引号，从0开始计数：

* 0 代表 'Crazing'
* 1 代表 'Inclusion'
* 2 代表 'Patches'
* 3 代表 'Pitted Surface'
* 4 代表 'Rolled-in Scale'
* 5 代表 'Scratches'

开始在 MacBook m3 上测试训练，使用 MPS 设备，也就是 M3 芯片的 GPU 能力：

```
python train.py \
  --img 416 \
  --batch 8 \
  --epochs 3 \
  --data dataset/data.yaml \
  --weights yolov5n.pt \
  --device mps
```

注意，会自动从 GitHub 中下载，需要连接代理：

```
# 设置HTTP HTTPS代理
export http_proxy=http://127.0.0.1:12334
export https_proxy=http://127.0.0.1:12334
export HTTP_PROXY=http://127.0.0.1:12334
export HTTPS_PROXY=http://127.0.0.1:12334


# 设置所有协议的代理
export all_proxy=http://127.0.0.1:12334
export ALL_PROXY=http://127.0.0.1:12334
```

## DATASET VIA GOOGLE DRIVE

1. 这里使用网页用户界面的方式
2. 这里将 dataset 文件夹压缩为 zip，方便网络传输

## COLAB YOLOV5 CONFIG

1. 在 Google Drive 中油煎新建 train\_yolov5.ipynb，双击进入 Colab 界面
2. “修改” “笔记本设置” “GPU” “保存”，切换到 Nvidia 显卡模式
3. 运行命令查看显卡驱动：

    ```
    !nvidia-smi
    ```
4. 查看 torch：

    ```
    import torch
    torch.__version__
    ```
5. 安装 torchvision：

    ```
    !pip3 install torchvision
    ```
6. 挂载 Google Drive：

    ```
    import os
    from google.colab import drive
    drive.mount('/content/drive')
    
    
    path = "/content/drive/My Drive"
    
    
    os.chdir(path)
    os.listdir(path)
    ```
7. 下载 YOLOv5:

    ```
    !git clone https://github.com/ultralytics/yolov5.git
    ```
8. 查看当前目录下面有哪些文件夹：

    ```
    ls
    ```

    这是我当时的输出：

    ```
    24-11-18_backup/  colab_yolov5/  com.feralinteractive.gridautosport_edition_android/  yolov5/
    ```
9. 进入：

    ```
    cd yolov5
    ```
10. 解压 dataset 并复制到：

    ```
    %cd /content/drive/MyDrive
    !unzip colab_yolov5/dataset.zip -d yolov5/
    ```
11. 进入 YOLOv5，安装依赖：

    ```
    pip install -r requirements.txt
    ```

## TRAIN VIA COLAB

为钢铁缺陷检测设置合适的参数：

```
!python train.py \
  --img 256 \
  --batch 64 \
  --epochs 100 \
  --data dataset/data.yaml \
  --weights yolov5n.pt \
  --workers 8 \
  --cache \
  --device 0 \
  --save-period 10
```

添加 `--save-period 10` 参数，即使连接中断，也可以从最近的保存点继续训练

**Note**

每个参数的含义：

1. `--img 256`
    * 设置输入图片的大小为256x256像素
    * 您的原始图片是200x200，会被调整到这个尺寸
2. `--batch 64`
    * 每次训练处理64张图片
    * 较大的batch size可以加快训练并提高稳定性
    * T4 GPU的显存足够处理这个大小
3. `--epochs 100`
    * 完整的数据集将被训练100遍
    * 每个epoch会处理所有训练图片一次
4. `--data dataset/data.yaml`
    * 指定数据集配置文件的位置
    * 包含类别信息和数据集路径
5. `--weights yolov5n.pt`
    * 使用YOLOv5-nano预训练模型
    * 这是最小的YOLOv5模型，适合简单的缺陷检测任务
6. `--workers 8`
    * 使用8个工作进程来加载数据
    * 可以加快数据读取速度
7. `--cache`
    * 将图片缓存到内存中
    * 可以加快训练速度，减少磁盘读取
8. `--device 0`
    * 使用第一个GPU设备
    * 在Colab中就是指定使用T4 GPU
9. `--save-period 10`
    * 每10个epochs保存一次模型
    * 防止训练中断导致进度丢失

这些参数组合在一起，适合：

* 小尺寸图片(200x200)
* 钢铁缺陷检测任务
* T4 GPU环境
* Colab免费版的使用场景

**Note**

**训练多少次是最好的 ？**

对于钢铁缺陷检测这样的专业领域任务，建议从以下几个方面考虑训练轮次：

1. 经验值建议：
    * 起始训练100轮观察趋势
    * 重点关注50-80轮后的性能变化
    * 如果发现模型在70-80轮后性能仍在提升，可以继续训练到150轮
2. 判断依据：
    * 观察训练过程中的loss变化
    * 查看验证集上的mAP（平均精确度）变化
    * 关注每类缺陷的检测准确率
    * 当这些指标趋于平稳时，可以考虑停止训练
3. 具体的观察点：
    * box\_loss（边界框损失）是否稳定
    * cls\_loss（分类损失）的变化趋势
    * 验证集性能是否不再提升
    * 是否出现过拟合现象

建议：

1. 先训练100轮
2. 保存训练过程的指标
3. 用验证集评估模型效果
4. 根据实际效果决定是否需要继续训练

将训练结果下载到本地：

接从Google Drive下载，所有文件都保存在：yolov5/runs/train/exp\*/

在原先的基础上继续训练：

1. 使用 `--resume` 参数继续最后一次的训练：

    ```
    !python train.py --resume
    ```

    这会自动找到最近的训练并继续。
2. 从特定的权重文件继续训练（更推荐）：

    ```
    !python train.py \
      --img 256 \
      --batch 64 \
      --epochs 150 \  # 新的总轮次
      --data dataset/data.yaml \
      --weights runs/train/exp*/weights/last.pt \  # 使用之前训练的权重
      --workers 8 \
      --cache \
      --device 0 \
      --save-period 10
    ```

注意事项：

* 使用 `last.pt` 继续训练会保持之前的训练状态
* 设置的epochs是新的总轮次，而不是额外增加的轮次
* 其他参数建议保持和之前一致

在继续训练前，建议先查看之前的训练效果，确定是否真的需要更多轮次的训练。

这些训练参数（数据增强、模型选择等）都是为了得到一个更好的模型权重。一旦训练完成，这些参数就被"烘焙"到了模型权重中。所以调参不影响导出 ONNX

转换命令可能是：

```
!python export.py --weights runs/train/exp*/weights/best.pt --include onnx --img 256
```

## 分析

根据 results.txt 文件内容 或 runs/train/exp\*/results.png 图片，和最后几轮的训练输出日志

### 第一次

```
!python train.py \
  --img 256 \
  --batch 64 \
  --epochs 100 \
  --data dataset/data.yaml \
  --weights yolov5n.pt \
  --workers 8 \
  --cache \
  --device 0 \
  --save-period 10
```

根据训练结果和曲线图，分析训练情况：

1. 整体训练效果：
    * 总体mAP50达到了0.745（74.5%）
    * mAP50-95达到0.383（38.3%）
    * 训练用时很短，仅0.165小时
2. 各类别的检测效果：
    * 效果最好的是Pitted Surface：准确率90.8%，召回率84.6%，mAP50达到93.7%
    * 效果最差的是Crazing：准确率29.9%，召回率49.2%，mAP50只有30.1%
    * 其他类别表现中等偏上，大多数mAP50在70-85%之间
3. 从损失曲线看：
    * box\_loss（定位损失）、obj\_loss（目标检测损失）和cls\_loss（分类损失）都在稳定下降
    * 验证集指标趋于平稳，没有明显的过拟合现象

**Note**

让我解释这些评估指标：

1. 准确率（Precision）= 29.9%
    * 在所有被模型预测为"Crazing"（裂纹）的检测框中，真正是裂纹的比例
    * 例如：如果模型检测了100个疑似裂纹，但只有30个是真的裂纹，准确率就是30%
    * 准确率低说明模型经常把其他缺陷误判为裂纹
2. 召回率（Recall）= 49.2%
    * 在所有真实的"Crazing"（裂纹）中，被模型成功检测出来的比例
    * 例如：如果图片中有100个真实裂纹，模型只检测出了49个，召回率就是49.2%
    * 召回率低说明模型经常漏检裂纹
3. mAP50 = 30.1%
    * mean Average Precision，平均精确度，是准确率和召回率的综合指标
    * 50表示IoU（交并比）阈值为0.5，即检测框与真实框重叠度超过50%才算正确检测
    * 30.1%的mAP50说明模型在检测裂纹时总体表现差

建议：

1. 针对Crazing类别的改进：
    * 可能需要增加该类别的训练样本
    * 考虑对该类别的数据增强
2. 是否需要继续训练：
    * 目前模型性能已经趋于稳定
    * 除非有特定的性能要求，否则继续训练收益可能不大

### 第二次

修改 data.yaml，添加权重配置，Crazing权重设为2，其他保持1：

```
path: dataset  # 数据集根目录
train: train/images  # 训练集图片相对路径
val: val/images      # 验证集图片相对路径
test: test/images    # 测试集图片相对路径


nc: 6   # 类别数量
names: ['Crazing', 'Inclusion', 'Patches', 'Pitted Surface', 'Rolled-in Scale', 'Scratches']  # 类别名称


class_weights: [2.0, 1.0, 1.0, 1.0, 1.0, 1.0]
```

使用更大的模型（yolov5s替代yolov5n）：

```
!python train.py \
  --img 256 \
  --batch 64 \
  --epochs 100 \
  --data dataset/data.yaml \
  --weights yolov5s.pt
```

根据训练结果和曲线图，分析训练情况：

1. Crazing类别的变化：
    * 准确率从29.9%提升到38.4%
    * 召回率从49.2%降到34.9%
    * mAP50从30.1%略微提升到32.1%
2. 各类别表现：
    * Patches表现最好：准确率82.9%，召回率92.2%，mAP50达到93.2%
    * Pitted Surface也很好：准确率88.3%，召回率84.6%，mAP50达到88%
    * Crazing仍然是最差的类别
3. 总体效果：
    * 整体mAP50达到70.1%
    * mAP50-95达到37.4%
    * 训练时间0.613小时

从结果来看：

1. 使用更大的模型和类别权重后：
    * Crazing的准确率有所提升
    * 但召回率反而下降了
    * 整体改善效果不明显

建议：

1. 数据方面的改进可能更有效：
    * 增加Crazing类别的样本数量
    * 对Crazing样本进行数据增强
    * 收集更多样的Crazing缺陷图片

### 第三次

对Crazing样本进行数据增强

在data.yaml中添加对Crazing类别的特定数据增强策略

首先创建一个新的hyp文件：

```
# 在yolov5目录下创建
!cp data/hyps/hyp.scratch-high.yaml data/hyps/hyp.steel.yaml
```

hyp.steel.yaml：

```
lr0: 0.01
lrf: 0.01
momentum: 0.937
weight_decay: 0.0005
warmup_epochs: 3.0
warmup_momentum: 0.8
warmup_bias_lr: 0.1
box: 0.05
cls: 0.5
cls_pw: 1.0
obj: 1.0
obj_pw: 1.0
iou_t: 0.2
anchor_t: 4.0
fl_gamma: 0.0
hsv_h: 0.015  # HSV-色调增强
hsv_s: 0.7    # HSV-饱和度增强
hsv_v: 0.4    # HSV-亮度增强
degrees: 20.0  # 图像旋转 (+/- deg)
translate: 0.2  # 平移 (+/- fraction)
scale: 0.5     # 图像缩放 (+/- gain)
shear: 0.0    # 图像剪切 (+/- deg)
perspective: 0.0  # 透视变换
flipud: 0.0    # 上下翻转概率
fliplr: 0.5    # 左右翻转概率
mosaic: 1.0    # mosaic增强概率
mixup: 0.1     # mixup增强概率
copy_paste: 0.0  # 分割拷贝概率
```

训练：

```
!python train.py \
  --img 256 \
  --batch 64 \
  --epochs 100 \
  --data dataset/data.yaml \
  --weights yolov5s.pt \
  --hyp data/hyps/hyp.steel.yaml
```

根据训练结果和曲线图，分析训练情况：

1. Crazing类别的变化：
    * 准确率从38.4%提升到52.8%（显著提升）
    * 召回率从34.9%提升到38%（略微提升）
    * mAP50从32.1%提升到34.9%
2. 其他类别表现：
    * Pitted Surface效果最好：准确率97.1%，召回率86.2%，mAP50达到93.9%
    * Patches也很好：准确率72.9%，召回率94.8%，mAP50达到92.1%
    * 所有类别的性能都有所提升
3. 总体效果：
    * 整体mAP50从70.1%提升到74.1%
    * 总体准确率达到72.7%
    * 总体召回率达到69.4%

数据增强的效果是明显的：

1. Crazing类别的提升最大，尤其是准确率
2. 其他类别也都得到了改善
3. 模型的泛化能力有所提升

建议：

1. 可以考虑进一步优化：
    * 针对性调整数据增强参数
    * 尝试更长的训练轮次
2. 或者考虑其他改进方向：
    * 收集更多Crazing样本
    * 尝试不同的模型架构