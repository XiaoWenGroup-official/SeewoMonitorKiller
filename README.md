# SeewoMonitorKiller
> [!IMPORTANT]
> 本项目(SeewoMonitorKiller)基于GPL-v3协议开源

SeewoMonitorKiller 是针对希沃希沃管家监控的反监控系统 最新版本V1.2（已发布）

由于班主任时不时通过希沃的巡课系统（俗称监控）来查看上课情况，这对学生来说当然不是一个好消息。因此，本反监控系统就是监控希沃，并在有异常情况时立即报告。

> [!CAUTION]
>
> 本项目最后更新于2024年12月6日，希沃管家相关行为机制可能已经更改，请在安全环境自行测试项目是否任然有效
>
> 本项目基于 “SeewoMonitorSystem” 原项目地址 https://github.com/DengHanxu/SeewoMonitorSystem

> [!WARNING]
>
> 本项目的思路构造以及极小部分源码借鉴SeewoMonitorSystem，由于GPL-v3协议有继承性，现基于GPL-v3开源

# 原理

希沃巡课系统主要依赖3个程序，`media_capture.exe`，`screenCapture.exe`，`rtcRemoteDesktop.exe`，而`media_capture.exe`就是负责获取摄像头数据的。
因此，不断查询当前进程列表并检查是否有`media_capture`就能大致确定（[这种方法并不完全准确](#局限性)）是否有老师正在看监控。

# 主要构成
- [SeewoMonitorKiller](##SeewoMonitorKiller)

## SeewoMonitorKiller

SeewoMonitorKiller整合SeewoMonitorSystem的部分代码及在此基础上增加部分功能，将负责监控进程及杀进程的功能以指示块和系统通知的形式显示

#版本介绍

### SeewoMonitorKiller-V1.0
这是第一个版本，默认以大约`1次/秒`的速度检测`media_capture.exe`，`screenCapture.exe`，`rtcRemoteDesktop.exe`。
当检测到`media_capture.exe`或`screenCapture.exe`或`rtcRemoteDesktop.exe`在运行时，就认为监控系统正在运行，并在屏幕上方正中的位置(已修复SeewoMonitorSystem显示偏左的问题)显示一个4x4像素的红/橙/蓝色方块。
当`media_capture.exe`或`screenCapture.exe`或`rtcRemoteDesktop.exe`结束运行时，就认为本次监控结束，对应方块消失。
> [!IMPORTANT]
> 当前版本已不建议使用，检测延迟比SeewoMonitorSystem还有多≥1.5s，经测试延迟0.6s-1.5s才会显示色块且并不显眼

>[!IMPORTANT]
>此程序已在代码中修改提权方式，不必像 [SeewoMonitorSystem](https://github.com/DengHanxu/SeewoMonitorSystem) 一样调用`Nsudo.exe`才能发挥作用
>有关`Nsudo.exe`，请参阅 [Nsudo](https://github.com/M2TeamArchived/NSudo)

# 使用本项目
## 打包成exe

1. 运行`pip.bat`安装依赖库
2. 使用`pyinstaller SeewoMonitor2.py -F -w`命令打包

> [!CAUTION]
> 使用`pyinstaller`打包的exe会被`WindowsDeferder`等报毒为特洛伊木马，程序本身并没有病毒

## 添加自启动（可选）

> [!IMPORTANT]
> 执行此操作前请确保`冰点还原`处于关闭状态，打开`explorer`，如果`C`盘是下图所示状态，则表明`C`盘被冻结，添加的自启动重启就会失效。
> 
> ![](docs/FreezedC.png)
> 
> 如果`冰点还原`是开启状态，你需要先关闭`冰点还原`。

具体如何添加自启动自行搜索引擎搜索。

# 局限性

[原理](#原理)中提到的3个程序有时候会自己启动，导致误报。不过1分钟后就会被清除。
目前尚不清楚相关机制。
