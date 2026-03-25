---
title: OpenCV在Qt下的配置方法
data: 2026-03-13
author: 空空杯
excerpt: 详情见帖子内部
categories: 
  - 芝士,与你分享
---
长话短说，步骤如下
## 一、下载对应软件
务必准备好以下几个软件
1. [OpenCV](https://github.com/opencv) (可以从tag中选取版本进行下载)
2. [Cmake](https://cmake.org/)(记得将Cmake的环境变量添加到系统路径中)
3. [OpenCV拓展库](https://github.com/opencv/opencv_contrib)(版本号要与OpenCV一致，可以不装)
4. [Qt](https://www.qt.io/zh-cn/)(可用镜像源对下载进行加速)
## 二、用Cmake对OpenCV的源代码进行编译
- 打开Cmake UI界面，在"where is the source code"中选择OpenCV的源码路径(示例：C:\opencv\sources)
- 在opencv的bulid文件夹中[^1]，新建文件夹Qt，然后在Cmake的"where to build the binaries"选择刚刚新建文件夹的路径
- 勾选advance，随后点击configure
示例如图所示：![Cmake配置示例](/img/OpenCV-Qt/1.png)
## 三、配置编译选项
- 点击config后，根据Qt安装时的编译器来进行Cmake编译器的选择(此处以Mingw为例)[^2]，然后选择"Specify native compilers"
- C的编译路径选择Qt下载文件夹下的 "..\Tools\mingw1310_64\bin\gcc.exe"
- C++的编译路径选择Qt下载文件夹下的 "..\Tools\mingw1310_64\bin\g++.exe"
- 点击finish，开始编译文件
## 四、再次配置编译选项
- 选择下方列表中的"WITH_QT","WITH_OPENGL"，如果有下载OpenCV拓展库，还须在"OPENCV_EXTRA_MODULES_PATH"添加路径"..\opencv_contrib-4.13.0\modules"
- 再次点击config，随后根据Qt的版本选择Cmake红色处的"Qt5_DIR"或"Qt6_DIR",路径选择"..\Qt\6.10.2\mingw_64\lib\cmake\Qt5"或者"..\Qt\6.10.2\mingw_64\lib\cmake\Qt6"(示例如下图所示)
- 再次点击config，完成后点击generate
![Qt路径选择](/img/OpenCV-Qt/2.png)
## 五、编译库文件
- 进入步骤二中的Qt文件夹，在该路径下打开cmd，输入
```
mingw32-make -j 8
```
- 完成后输入
```
mingw32-make install
```
- 在Qt项目中的.pro文件中添加
```
INCLUDEPATH += C:\opencv\build\x64\Qt\install\include //编译完成后的include路径
LIBS += C:\opencv\build\x64\Qt\lib\libopencv_*.a //编译完成后的lib文件路径
```
![命令行快速打开指定路径方法](/img/OpenCV-Qt/3.png)
## 六、测试
测试代码如下
``` C++
//mainwindow.cpp文件
#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/imgproc/imgproc.hpp>
using namespace cv;

MainWindow::MainWindow(QWidget *parent)
    : QMainWindow(parent)
    , ui(new Ui::MainWindow)
{
    ui->setupUi(this);
    Mat image=imread(“E:\Fighting.jpg”,1);//一定要使用绝对路径，其他可以回报错
    namedWindow( “Display window”, WINDOW_AUTOSIZE );
    imshow( “Display window”, image );
}

MainWindow::~MainWindow()
{
    delete ui;
}
```
能打开指定路径图片文件，则说明库安装成功。

[^1]: 其他文件夹也行，确保后续Qt编译路径时能找到Cmake generate后文件

[^2]: 不确定自己编译器的话MaintenanceTool.exe查看自己的编译器，一般有两种，一种是Mingw，一种是MSVC