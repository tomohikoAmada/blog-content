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

## 第二部分: Windows安装

### 6\. 安装Windows 11

1. 插入准备好的Windows安装U盘
2. 重启电脑,进入启动选择菜单:
    * 查看开机时屏幕提示的按键(通常显示"Press X to enter boot menu")
    * ThinkPad一般是F12
    * Dell一般是F12
    * HP一般是F9
    * 其他品牌可能是F2/Del/Esc
    * 如果不确定,可以在开机时反复按F12试试
3. 在启动菜单中:
    * 找到你的U盘(通常带有"UEFI"字样)
    * 一定要选择带"UEFI"字样的启动项
    * 用方向键选中并按Enter
4. 进入Windows安装界面:
    * 选择语言:"中文(简体)"
    * 区域:"中国"
    * 点击"下一步"
    * 点击"立即安装"
    * 如果提示输入产品密钥,选择"我没有产品密钥"
    * 选择系统版本:"Windows 11 家庭中文版"
    * 接受许可条款
    * 选择"自定义: 仅安装Windows(高级)"
5. 在分区界面:
    * 你会看到之前预留的"未分配空间"
    * **重要**: 不要点击或修改任何现有分区
    * 选中"未分配空间"
    * 点击"新建"
    * 保持默认大小,点击"应用"
    * Windows会自动创建所需的系统分区
    * 选择最大的那个新建分区
    * 点击"下一步"
6. 等待安装完成
    * 这个过程可能需要20-30分钟
    * 期间电脑会重启几次
    * **重要**: 让它自动重启,不要按任何按键
7. 建议在 Windows 安装完成后，先不要立即修复 GRUB
    先在 Windows 中完成所有初始设置和更新
    特别是确保 Windows 完成所有系统更新后再修复引导

## 第三部分: 修复启动引导

### 7\. 准备修复环境

安装完Windows后,电脑可能直接进入Windows系统,这是正常的。我们需要修复启动引导:

1. 准备Ubuntu启动U盘
    * 如果没有,需要在另一台电脑上制作一个
    * 可以用Ubuntu官网下载ISO文件制作
2. 从Ubuntu U盘启动:
    * 重启电脑,进入启动菜单(同上述步骤)
    * 选择Ubuntu U盘(带UEFI字样的)
    * 选择"Try Ubuntu without installing"
3. 连接网络
    * 点击右上角WiFi图标
    * 连接到可用网络

### 8\. 修复GRUB引导

```bash
# 安装修复工具
sudo add-apt-repository ppa:yannubuntu/boot-repair
sudo apt update
sudo apt install -y boot-repair


# 运行修复工具
boot-repair
```

在boot-repair界面:

1. 点击"推荐修复"
2. 等待工具完成修复
3. 它会生成一个网址,包含修复日志
4. 建议把这个网址保存下来

### 9\. 如果boot\-repair失败\,使用手动修复方式:

```bash
# 确定Ubuntu安装分区
sudo fdisk -l


# 挂载必要的分区(根据实际分区号修改)
sudo mount /dev/sda2 /mnt  # Ubuntu根分区
sudo mount /dev/sda1 /mnt/boot/efi  # EFI分区
sudo mount --bind /dev /mnt/dev
sudo mount --bind /proc /mnt/proc
sudo mount --bind /sys /mnt/sys


# 进入chroot环境
sudo chroot /mnt


# 更新并重新安装grub
update-grub
grub-install /dev/sda


# 退出chroot
exit


# 取消挂载
sudo umount /mnt/sys
sudo umount /mnt/proc
sudo umount /mnt/dev
sudo umount /mnt/boot/efi
sudo umount /mnt
```

### 10\. 系统时间同步修复

```
# 在Ubuntu中执行
timedatectl set-local-rtc 1
```