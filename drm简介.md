# drm简介
## 1. DRM 基础显示系统介绍

### drm总览图:
![image](https://github.com/Feng3333/DRM/blob/49d1206edf068c63d32abb2e5f2276c6ddebfecd/drm_images/drm%E6%80%BB%E8%A7%88%E5%9B%BE.png)

### drm基本概念

![image](https://github.com/Feng3333/DRM/blob/49d1206edf068c63d32abb2e5f2276c6ddebfecd/drm_images/drm%E6%A1%86%E6%9E%B6%E5%9B%BE1.png)

DRM（ Direct Rendering Manager）即直接渲染管理器。它是为了解决多个程序对 Video 硬件资源的协同使用问题而产生的，可以将它简单理解为一个仲裁器的作用。它也解决了原先Linux fbdev 无法满足对基于3D加速GPU的视频硬件控制需求的问题。

DRM 框架向用户空间提供了一组 API，提供了一系列的 ioctl 操作，允许用户程序直接访问操纵 GPU以及显示模式的配置。即这两类行为：Graphics Execution Manager (GEM)、Kernel Mode-Setting (KMS)。前者 GEM 主要是对 FrameBuffer 的管理，如显存的申请释放 (Framebuffer managing) ，显存共享机制 (Memory sharing objects)， 及显存同步机制 (Memory synchronization)，而后者 KMS 主要是完成显卡配置 (Display mode setting) 。


### libdrm
libdrm 是个跨驱动中间件，帮助用户空间程序通过DRI协议与内核DRM Core通讯。即将drm 提供的系列 ioctl 接口进一步封装，方便用户使用。

### KMS 
KMS 表示 Kernel Mode Setting，是DRM API的一个子部件。用来设置显卡的模式，比如屏幕分辨率、色深、刷新率、显示通路（pipeline）选择等。KMS 也是为了解决多个模块同时进行显卡模式设置容易冲突的问题，将原先的User space Mode setting 移到 Kernel中进行统一管理，并入到DRM module中。

### kms设备模型

KMS 将输出设备管理和模型化成一系列抽象的硬件模块，可以在显示控制器的显示输出管道（display output pipeline）中看到 。pipeline 一般是按这样连接的：framebuffer → plane → crtc → encoder → connector → panel。每个模块解释如下：

crtc （CRT controller) 表示一个扫描输出引擎。Rockchip 这里一般指 vop (Video output processer), 一般一个屏幕对应一个crtc。

connector 表示 video 信号输出的地方，通常就是屏幕接口，如VGA，HDMI, DP等。

encoder: 需要将crtc 输出的 video 时序信号编码成对应接口的格式。如TMDS、LVDS等。

plane 是个内存对象，包含一个将要输出给crtc 的buffer。

panel 就是指显示屏。
