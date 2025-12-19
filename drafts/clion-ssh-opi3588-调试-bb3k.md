---
title: "CLion ssh OPi3588 调试 BB3k"
date: 2025-12-19
category: "技术"
tags: ["RK3588", "BB3k"]
draft: true
---

# CLion ssh OPi3588 调试 BB3k

## CLion 中设置

1. Settings/Preferences → Build, Execution, Deployment → Toolchains
2. 添加Remote Host类型的工具链
3. 配置SSH连接信息（IP、用户名、密码/密钥）
4. CLion会自动检测远程的CMake、gcc/g++、gdb等工具

**还可以进行 CMake 配置：**

* Settings → Build, Execution, Deployment → CMake
* 选择刚才创建的远程工具链
* 配置Build目录（可以是远程路径）

## 配置环境

```bash
sudo apt update
sudo apt install -y build-essential cmake git
sudo apt install -y gcc g++ gdb
sudo apt install -y libopencv-dev
sudo apt install -y qt6-base-dev
sudo apt install -y libv4l-dev v4l-utils
sudo apt install -y libpthread-stubs0-dev
sudo apt install -y qt6-base-dev qt6-multimedia-dev
sudo apt install -y libgl1-mesa-dev libglu1-mesa-dev
sudo apt install -y libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev
sudo apt install -y gstreamer1.0-plugins-base gstreamer1.0-plugins-good \
                    gstreamer1.0-plugins-bad gstreamer1.0-plugins-ugly \
                    gstreamer1.0-libav gstreamer1.0-tools
```

期间遇到 PulseAudio 的客户端配置文件，GStreamer 依赖引发，选择 **N**（或直接按回车使用默认值）

**然后编译：**

```bash
cd /tmp/tmp.8yOa6izPM8/BatixBrain3kDemo/build
rm -rf *
cmake -DCMAKE_PREFIX_PATH=/usr/lib/aarch64-linux-gnu/cmake/Qt6 ..
```

## 结果

```bash
orangepi@orangepi5plus:/tmp/tmp.8yOa6izPM8/BatixBrain3kDemo/build$ cd /tmp/tmp.8yOa6izPM8/BatixBrain3kDemo/build
orangepi@orangepi5plus:/tmp/tmp.8yOa6izPM8/BatixBrain3kDemo/build$ rm -rf *
orangepi@orangepi5plus:/tmp/tmp.8yOa6izPM8/BatixBrain3kDemo/build$ cmake -DCMAKE_PREFIX_PATH=/usr/lib/aarch64-linux-gnu/cmake/Qt6 ..
-- The C compiler identification is GNU 11.4.0
-- The CXX compiler identification is GNU 11.4.0
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Performing Test HAVE_STDATOMIC
-- Performing Test HAVE_STDATOMIC - Success
-- Found OpenGL: /usr/lib/aarch64-linux-gnu/libOpenGL.so   
-- ✓ Found Qt6: 6.2.4
-- ✓ Found OpenCV: 4.5.4
-- Found PkgConfig: /usr/bin/pkg-config (found version "0.29.2") 
-- Checking for module 'gstreamer-1.0'
--   Found gstreamer-1.0, version 1.20.3
-- Checking for module 'gstreamer-app-1.0'
--   Found gstreamer-app-1.0, version 1.20.1
-- Checking for module 'glib-2.0'
--   Found glib-2.0, version 2.72.4
-- Checking for module 'gobject-2.0'
--   Found gobject-2.0, version 2.72.4
-- ✓ Found GStreamer: 1.20.3
-- Found Threads: TRUE  
-- ✓ Linked spdlog (system library)
-- 
-- =================================
-- 📋 BatixBrain3k Configuration
-- =================================
-- OpenCV:          4.5.4
-- FrameGrabber:    Enabled
-- Qt6:             6.2.4
-- GStreamer:       1.20.3
-- Executables:     BatixBrain3kDemo, test_inference, test_framegrabber, test_framegrabber_linux
-- Build Type:      
-- C++ Standard:    17
-- spdlog:          Enabled
-- =================================
-- 
-- Configuring done
-- Generating done
-- Build files have been written to: /tmp/tmp.8yOa6izPM8/BatixBrain3kDemo/build
```

## [对话地址](https://claude.ai/share/b65b66e1-2b4b-4751-a702-c1eb2a75af11)


