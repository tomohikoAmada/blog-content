---
title: "OrangePi5Plus 硬件显示器初始不显示"
date: 2026-01-11
category: "技术"
tags: ["RK3588"]
draft: false
---

# OrangePi5Plus 硬件显示器初始不显示

检查结果：

```bash
orangepi@orangepi5plus:/home$ xrandr
Screen 0: minimum 320 x 200, current 1024 x 600, maximum 16384 x 16384
HDMI-1 disconnected primary (normal left inverted right x axis y axis)
HDMI-2 connected 1024x600+0+0 (normal left inverted right x axis y axis) 0mm x 0mm
   1024x600      59.82*+
   1920x1080     60.00    50.00    50.00    59.94  
   1920x1080i    60.00    60.00    50.00    50.00    59.94  
   1280x1024     75.02  
   1280x720      60.00    50.00    50.00    59.94  
   1024x768      75.03    70.07    60.00  
   832x624       74.55  
   800x600       72.19    75.00    60.32    56.25  
   720x576       50.00    50.00    50.00  
   720x480       60.00    60.00    59.94    59.94  
   640x480       75.00    72.81    60.00    59.94  
   720x400       70.08  
DP-1 disconnected (normal left inverted right x axis y axis)

orangepi@orangepi5plus:/home$ ps aux | grep -E "X|wayland|display"
root        1633  0.7  2.0 2089360 166140 tty7   Ssl+ 23:19   0:02 /usr/lib/xorg/Xorg -core :0 -seat seat0 -auth /var/run/lightdm/root/:0 -nolisten tcp vt7 -novtswitch
orangepi    5703  0.0  0.0  19808  1860 pts/0    S+   23:25   0:00 grep --color=auto -E X|wayland|display

orangepi@orangepi5plus:/home$ journalctl -xe | grep -i display
Jan 11 23:19:03 orangepi5plus systemd[1]: Starting Light Display Manager...
Jan 11 23:19:05 orangepi5plus systemd[1]: Started Light Display Manager.

orangepi@orangepi5plus:/home$ ls /sys/class/drm/
card0  card0-DP-1  card0-HDMI-A-1  card0-HDMI-A-2  card0-Writeback-1  card1  renderD128  renderD129  version

orangepi@orangepi5plus:/home$ cat /sys/class/drm/card*/status
disconnected
disconnected
connected
unknown

orangepi@orangepi5plus:/home$ ls -la /dev/fb*
crw-rw---- 1 root video 29, 0 Jan 11 23:19 /dev/fb0

orangepi@orangepi5plus:/home$ lsmod | grep -i mali

orangepi@orangepi5plus:/home$ lsmod | grep -i panfrost

orangepi@orangepi5plus:/home$ glxinfo | grep -i vendor
libGL error: glx: failed to create dri3 screen
libGL error: failed to load driver: rockchip
libGL error: glx: failed to create dri2 screen
libGL error: failed to load driver: rockchip
server glx vendor string: SGI
client glx vendor string: Mesa Project and SGI
    Vendor: Mesa (0xffffffff)
OpenGL vendor string: Mesa
```

显示器实际上**已经连接并被识别了**，只是分辨率设置不对：

从 `xrandr` 输出可以看到：

* **HDMI-2** 已连接（connected）
* 当前分辨率是 **1024x600**（这是个很低的分辨率）
* 你的显示器支持 1920x1080 等更高分辨率

这个 1024x600 分辨率可能导致显示器显示异常或者超出可视范围。

设置为标准1080p分辨率：

```bash
xrandr --output HDMI-2 --mode 1920x1080 --rate 60
```

永久设置：

如果上述命令让显示器正常显示了，可以创建配置文件让设置永久生效：

```bash
# 创建xrandr配置脚本
mkdir -p ~/.config/autostart
nano ~/.config/autostart/monitor-setup.desktop
```

添加以下内容：

```ini
[Desktop Entry]
Type=Application
Name=Monitor Setup
Exec=xrandr --output HDMI-2 --mode 1920x1080 --rate 60
X-GNOME-Autostart-enabled=true
```

保存后，重启系统配置就会自动生效。

另外注意到的问题：

GPU 驱动有些问题（`libGL error: failed to load driver: rockchip`），虽然不影响基本显示，但可能影响 3D 加速。如果需要解决，可以安装：

```bash
sudo apt update
sudo apt install mesa-utils libgl1-mesa-dri
```