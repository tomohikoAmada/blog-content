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