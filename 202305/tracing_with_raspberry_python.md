# 一步步学机器人 #

## 简介 ##
让自己组装的机器人动起来而且能循迹，需要不少知识- 机械组装，电子电路，软件编程，算法和自动化技术。让我们一步步让我们自己做的机器人像买来的成品一样运动起来吧。

### raspberry 树莓派4B ###
这是机器人的大脑，负责处理摄像头送回来的图像。我们要熟悉树莓派的操作。它是基于Linux 的操作系统。在家里电脑上我们可以安装Ubuntu20.04 这样的系统来训练和编程。
### Linux桌面操作系统 ###
可以寻求爸爸妈妈或者教练以及朋友的帮助来安装和使用Ubuntu桌面系统。
### 安装Python3 ###
虽然openCV 曾经依赖Python2 来实现机器视觉处理，但是现在已经可以切换到Python3了

python --version
sudo apt update
sudo apt install python3.8
python --version

### 代码调试环境Pycharm ###
我们需要一个图形化调试环境来让工作更直观-Pycharm不错。
安装：
sudo snap install pycharm-community --classic
运行：
pycharm-community
如果出错"ModuleNotFoundError: No module named 'distutils.core'"
```
sudo apt-get install python3-distutils
```
### 获取openCV 驱动库 ###
为了对图形进行放缩、灰度、色彩变换，需要机器视觉代码库，openCV4 开源库可以用来学习。
安装：
sudo apt install libopencv-dev python3-opencv
确认：
python3 -c "import cv2; print(cv2.__version__)"
### 利用openCV显示静态图像 ###

### 连接摄像头显示视频 ###
这有一点复杂了。我们参考树莓派官网的例子来做。python+picamera。
（1）、 打开usb摄像头读取一张照片

import CV2

import matplotlib.pyplot as plt

#opencv调用csi摄像头优先0，然后usb按顺序排列下去

capture = CV2.VideoCapture(0)

# 获取一帧

ret, frame = capture.read()

plt.imshow(frame[:,:,::-1])#BGRtoRGB

plt.show()

# 释放资源

capture.release() 

### 还是图像 ###
虽然摄像头作用很大，但是在机器视觉领域处理对象依旧是一帧帧的图像。对图像进行编程处理是我们最重要的目标。首先，直觉告诉我们需要判断当前捕获的图像的内容- 它是竞赛中26幅图当中哪一个呢？或者一个都不是呢？这怎么判断？正确率是多少？需要将摄像头输出的照片进行变换和匹配。
图像在Python里是三通道张量-在图像处理空间的每个点（采样值）都是一个三维变量。这样就形成数学上所谓的张量。计算时，张量变成一维数组。计算完成后再恢复成二阶张量。