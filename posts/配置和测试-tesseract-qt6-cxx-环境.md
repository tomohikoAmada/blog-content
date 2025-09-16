---
title: "配置和测试 Tesseract Qt6 C++ 环境"
date: 2025-09-16
category: "技术"
tags: ["Tesseract", "Qt6", "C++"]
draft: false
---

## 配置和测试 Tesseract Qt6 C++ 环境

* Windows
    1. 下载Tesseract：
        * 访问 https://github.com/UB-Mannheim/tesseract/wiki
        * 下载适合您系统的Tesseract安装程序（32位或64位）。
    2. 安装Tesseract：
        * 运行下载的安装程序。
        * 记住安装路径（默认通常是 `C:\Program Files\Tesseract-OCR`）。
    3. 设置环境变量：
        * 右击"此电脑"，选择"属性" > "高级系统设置" > "环境变量"。
        * 在"系统变量"中，编辑"Path"，添加Tesseract的bin目录（如 `C:\Program Files\Tesseract-OCR`）。
    4. 下载Tesseract开发文件：
        * 返回Tesseract下载页面。
        * 下载对应版本的开发文件（通常名为 `tesseract-ocr-w64-setup-v5.x.x.x-dev.exe`）。
        * 安装这些开发文件。
    5. 配置 qmake

        ```
        QT       += core
        QT       -= gui
        
        
        CONFIG   += console
        CONFIG   -= app_bundle
        
        
        TEMPLATE = app
        
        
        SOURCES += main.cpp
        
        
        # Tesseract配置
        INCLUDEPATH += "C:/Program Files/Tesseract-OCR/include"
        LIBS += -L"C:/Program Files/Tesseract-OCR/lib" -ltesseract52
        
        
        # Leptonica配置
        INCLUDEPATH += "C:/Program Files/Tesseract-OCR/include/leptonica"
        LIBS += -L"C:/Program Files/Tesseract-OCR/lib" -lleptonica-1.82.0
        
        
        # 确保可执行文件能找到DLL
        QMAKE_POST_LINK += $$quote(cmd /c copy /y "C:\\Program Files\\Tesseract-OCR\\*.dll" "$$OUT_PWD")
        ```
    6. 编写测试代码

        ```
        #include <QCoreApplication>
        #include <QDebug>
        #include <tesseract/baseapi.h>
        #include <leptonica/allheaders.h>
        
        
        int main(int argc, char *argv[])
        {
            QCoreApplication a(argc, argv);
        
        
            tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
            if (api->Init(NULL, "eng")) {
                qCritical() << "Could not initialize tesseract.";
                return 1;
            }
        
        
            Pix *image = pixRead("path_to_your_image.png");
            api->SetImage(image);
            char* outText = api->GetUTF8Text();
            qDebug() << "OCR output:\n" << outText;
        
        
            api->End();
            delete api;
            delete[] outText;
            pixDestroy(&image);
        
        
            return a.exec();
        }
        ```
    7. 添加语言数据：
        * 将 `C:\Program Files\Tesseract-OCR\tessdata` 中的语言文件（如 eng.traineddata, chi\_sim.traineddata）复制到你的项目输出目录下的 tessdata 文件夹中。
    8. 构建和运行：
        * 在Qt Creator中构建并运行项目。
        * 如果遇到 DLL 无法找到的错误，确保所有必要的 DLL 都在可执行文件的同一目录下。

    注意事项：
    * 确保所有路径匹配你的实际安装位置。
    * 如果使用64位版本，路径可能需要调整（例如，"Program Files (x86)" 而不是 "Program Files"）。
    * 某些版本的Tesseract可能需要稍微不同的库名称，请根据实际情况调整。

    常见问题解决：
    1. 如果遇到链接错误，检查库名称是否正确，路径是否正确设置。
    2. 如果运行时出现 DLL 无法找到的错误，确保所有必要的 DLL 都被复制到了输出目录。
    3. 如果OCR结果不正确，检查语言数据文件是否正确放置
* Ubuntu
    1. 安装 Tesseract OCR 和开发库：

        ```
        sudo apt update
        sudo apt install tesseract-ocr
        sudo apt install libtesseract-dev
        sudo apt install libleptonica-dev
        ```
    2. 安装所需的语言数据：

        ```
        sudo apt install tesseract-ocr-eng  # 英文
        sudo apt install tesseract-ocr-chi-sim  # 简体中文
        ```
    3. 验证 Tesseract 安装：

        ```
        tesseract --version
        tesseract --list-langs
        ```
    4. 安装 Qt6 开发工具（如果尚未安装）：

        ```
        sudo apt install qt6-base-dev
        sudo apt install qt6-tools-dev
        ```
    5. 在 Qt Creator 中配置项目：
        1. 创建新的 Qt 应用程序或打开现有项目
        2. 编辑项目的 .pro 文件，添加 Tesseract 库：

            ```
            QT       += core gui
            
            
            greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
            
            
            CONFIG += c++17
            
            
            # 添加 Tesseract 库
            LIBS += -ltesseract -llept
            
            
            # 指定头文件路径
            INCLUDEPATH += /usr/include/tesseract /usr/include/leptonica
            
            
            # 你的源文件
            SOURCES += \
                main.cpp \
                mainwindow.cpp
            
            
            HEADERS += \
                mainwindow.h
            
            
            FORMS += \
                mainwindow.ui
            
            ```
        3. 编写测试代码:

            ```
            #include <QCoreApplication>
            #include <QDebug>
            #include <tesseract/baseapi.h>
            #include <leptonica/allheaders.h>
            
            
            int main()
            {
                tesseract::TessBaseAPI *api = new tesseract::TessBaseAPI();
                if (api->Init(NULL, "eng+chi_sim")) {
                    qDebug() << "Could not initialize tesseract.";
                    return 1;
                }
            
            
                // 这里替换为你的图像路径
                Pix *image = pixRead("/home/bozhouli/下载/testocr.jpg");
                api->SetImage(image);
            
            
                char* outText = api->GetUTF8Text();
                qDebug() << "OCR output:\n" << outText;
            
            
                api->End();
                delete api;
                delete [] outText;
                pixDestroy(&image);
            
            
                return 0;
            }
            ```

**Caution**

这些文件被下载到了哪里?

Windows:

1. 默认安装目录：
    * 程序文件：`C:\Program Files\Tesseract-OCR`（64位系统）
    * 或 `C:\Program Files (x86)\Tesseract-OCR`（32位系统）
2. 可执行文件：
    * `C:\Program Files\Tesseract-OCR\tesseract.exe`
3. 语言数据文件：
    * `C:\Program Files\Tesseract-OCR\tessdata\`
4. 开发库和头文件：
    * 头文件：`C:\Program Files\Tesseract-OCR\include\tesseract`
    * 库文件：`C:\Program Files\Tesseract-OCR\lib`

Linux (Ubuntu/Debian):

1. 可执行文件：
    * `/usr/bin/tesseract`
2. 语言数据文件：
    * `/usr/share/tesseract-ocr/4.00/tessdata/` （版本号可能会有所不同）
3. 开发库和头文件：
    * 头文件：`/usr/include/tesseract`
    * 库文件：`/usr/lib/` 或 `/usr/lib/x86_64-linux-gnu/`（64位系统）

验证安装位置：

1. 对于可执行文件，您可以在命令行中使用

    ```
    which
    ```

    命令（Unix系统）或

    ```
    where
    ```

    命令（Windows）：

    ```
    which tesseract  # Unix (macOS/Linux)
    where tesseract  # Windows
    ```
2. 对于语言数据文件，您可以使用 Tesseract 的命令行选项：

    ```
    tesseract --list-langs
    ```

## Develop Tesseract with OpenCV

#### 模板:

* qmake

    ```
    TEMPLATE = app          # 这行指定了项目类型为应用程序（app）。这意味着 qmake 将生成一个可执行文件，而不是库。
    CONFIG += console c++17 # console：指定这是一个控制台应用程序; c++17：设置 C++ 标准为 C++17。
    CONFIG -= app_bundle    # -app_bundle：在 macOS 上不创建应用程序包
    CONFIG -= qt            #  -qt：不使用 Qt 库（这是一个纯 C++ 项目）。
    
    
    # 这行链接 Tesseract 和 Leptonica 库。
    LIBS += -ltesseract -llept
    
    
    # OpenCV 库
    LIBS += -lopencv_core -lopencv_imgproc -lopencv_imgcodecs -lopencv_photo
    
    
    # 头文件路径
    # 这些行添加了头文件搜索路径，以便编译器能找到 Tesseract、Leptonica 和 OpenCV 的头文件。
    INCLUDEPATH += /usr/include/tesseract
    INCLUDEPATH += /usr/include/leptonica
    INCLUDEPATH += /usr/local/include/opencv4
    
    
    # 库文件路径
    # 这行添加了一个库搜索路径，告诉链接器在 /usr/local/lib 中寻找库文件。
    LIBS += -L/usr/local/lib
    
    
    # pkg-config
    # 这两行启用了 pkg-config 支持，并指定使用 opencv4 包。pkg-config 是一个帮助编译应用程序和库的工具，它能自动提供正确的编译选项。
    CONFIG += link_pkgconfig
    PKGCONFIG += opencv4
    
    
    SOURCES += \
            main.cpp
    ```

    如果想链接 OpenCV 所有的库, 这样修改 qmake 文件:

    ```
    TEMPLATE = app
    CONFIG += console c++17
    CONFIG -= app_bundle
    CONFIG -= qt
    
    
    # Tesseract 库
    LIBS += -ltesseract -llept
    
    
    # OpenCV 库
    CONFIG += link_pkgconfig
    PKGCONFIG += opencv4
    
    
    # 头文件路径
    INCLUDEPATH += /usr/include/tesseract
    INCLUDEPATH += /usr/include/leptonica
    
    
    # 如果 pkg-config 没有正确设置 OpenCV 的包含路径，可以取消下面这行的注释
    # INCLUDEPATH += /usr/local/include/opencv4
    
    
    SOURCES += \
            main.cpp
    
    
    # 使用 pkg-config 获取所有 OpenCV 库
    LIBS += $$system(pkg-config --libs opencv4)
    ```