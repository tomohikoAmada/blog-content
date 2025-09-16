---
title: "在 Ubuntu24.04 旁安装 Windows, 预留共享空间"
date: 2025-09-16
category: "教程"
tags: []
draft: true
---

# 在 Ubuntu24.04 旁安装 Windows, 预留共享空间

> Ubuntu 220GB, windows 220GB, 共享分区 30GB

## 第一部分: 准备工作

### 1\. 系统信息确认

首先确认当前系统的关键信息:

```bash
# 检查系统启动模式(UEFI还是Legacy)
[ -d /sys/firmware/efi ] && echo "UEFI" || echo "Legacy"


# 查看磁盘分区情况
lsblk


# 查看硬盘剩余空间
df -h


# 详细记录分区信息，包含分区UUID和文件系统类型
sudo lsblk -f > ~/system_backup/partition_details.txt


# 记录完整的分区表布局
sudo fdisk -l > ~/system_backup/partition_layout.txt


# 查看EFI启动项(如果是UEFI系统)
sudo efibootmgr -v
```

### 2\. 重要数据备份

在开始任何操作前,必须做好备份:

```
# 创建一个备份文件夹
mkdir ~/system_backup


# 备份已安装的软件列表
dpkg --get-selections > ~/system_backup/installed-software.txt


# 备份GRUB配置
sudo cp /etc/default/grub ~/system_backup/grub_backup
sudo cp -r /etc/grub.d ~/system_backup/grub.d_backup


# 备份当前分区表
sudo fdisk -l > ~/system_backup/partition_info.txt
sudo sfdisk -d /dev/sda > ~/system_backup/partition_table_backup.txt


# 备份重要的个人文件(根据需要修改路径)
cp -r ~/Documents ~/system_backup/
cp -r ~/Pictures ~/system_backup/
cp -r ~/Downloads ~/system_backup/
cp -r ~/.config ~/system_backup/
```

把这些备份文件复制到外部硬盘或U盘中保存。

### 3\. 准备Windows安装U盘

1. 准备一个容量至少16GB的U盘 (会被格式化,请提前备份U盘中的资料)
2. 在另一台Windows电脑上:
    * 访问微软官方下载页面: [https://www.microsoft.com/software-download/windows11](https://www.microsoft.com/software-download/windows11)
    * 下载"Media Creation Tool"
    * 运行工具,选择"为另一台电脑创建安装介质(U盘、DVD 或 ISO 文件)"
    * 选择语言:"中文(简体)"
    * 版本选择默认的"Windows 11"
    * 选择"USB闪存驱动器"
    * 等待制作完成(可能需要20-30分钟)

### 4\. 准备分区空间

```
# 安装分区工具
sudo apt update
sudo apt install gparted


# 关闭swap分区(为了安全调整分区)
sudo swapoff -a


# 启动分区工具
sudo gparted
```

在GParted界面操作:
在GParted界面操作:

1. 在界面上方选择正确的硬盘(在你的情况下是/dev/nvme0n1)
2. **重要**: 截图或记录下现有分区信息
3. 找到Ubuntu系统分区(nvme0n1p2, ext4格式)
4. 右键点击该分区,选择"调整大小/移动"
5. 调整分区大小：
    * 将Ubuntu分区缩小到220GB
    * **重要**：不要移动分区起始位置
    * 只拖动右侧边界
    * 这会产生约250GB的未分配空间
6. 在未分配空间中创建共享分区：
    * 右键点击最右侧的未分配空间
    * 选择"新建"
    * 大小设置为30GB
    * 文件系统选择"ntfs"
    * 标签(Label)设置为"Shared"
    * 点击"添加"
7. 此时应该还剩220GB未分配空间(给Windows使用)
8. 点击"应用所有操作"按钮
9. 等待完成（可能需要较长时间）

最终分区布局会是：

* nvme0n1p1: 1GB (EFI分区)
* nvme0n1p2: 220GB (Ubuntu系统)
* 新建的30GB NTFS分区（共享分区）
* 220GB未分配空间（留给Windows）

### 5\. 检查分区

```bash
# 检查分区完整性(将sdaX替换为你的Ubuntu分区号)
sudo e2fsck -f /dev/sdaX


# 再次确认分区情况
lsblk
```


