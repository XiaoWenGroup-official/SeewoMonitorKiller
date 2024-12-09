# SeewoMonitorkiller
SeewoMonitorKiller 是针对希沃希沃管家监控的反监控系统 最新版本V1.2（已发布）
# SeewoMonitorKiller

由于班主任时不时通过希沃的巡课系统（俗称监控）来查看上课情况，这对学生来说当然不是一个好消息。因此，本反监控系统就是监控希沃，并在有异常情况时立即报告。

> [!CAUTION]
>
> 本项目最后更新于2024年12月6日，希沃管家相关行为机制可能已经更改，请在安全环境自行测试项目是否任然有效
>
> 本项目基于 “SeewoMonitorSystem” 原项目地址 https://github.com/DengHanxu/SeewoMonitorSystem

> [!WARNING]
>
> 本项目的思路构造以及极小部分源码借鉴SeewoMonitorSystem，由于GPL-v3协议有继承性，先基于GPL-v3开源

# 原理

希沃巡课系统主要依赖3个程序，`media_capture.exe`，`screenCapture.exe`，`rtcRemoteDesktop.exe`，而`media_capture.exe`就是负责获取摄像头数据的。
因此，不断查询当前进程列表并检查是否有`media_capture`就能大致确定（[这种方法并不完全准确](#局限性)）是否有老师正在看监控。

# 主要构成
- [SeewoMonitorKiller](#SeewoMonitorKiller)

## SeewoMonitorKiller

SeewoMonitorKiller整合SeewoMonitorSystem的部分代码及在此基础上增加部分功能，将负责监控进程及杀进程的功能以指示块和系统通知的形式显示

### SeewoMonitorKiller-V1.0
这是第一个版本，默认以大约`1次/秒`的速度检测`media_capture.exe`，`screenCapture.exe`，`rtcRemoteDesktop.exe`。
当检测到`media_capture.exe`或`screenCapture.exe`或`rtcRemoteDesktop.exe`在运行时，就认为监控系统正在运行，并在屏幕上方正中的位置(已修复SeewoMonitorSystem显示偏左的问题)显示一个4x4像素的红/橙/蓝色方块。
当`media_capture.exe`或`screenCapture.exe`或`rtcRemoteDesktop.exe`结束运行时，就认为本次监控结束，对应方块消失。
> [!IMPORTANT]
> 当前版本已不建议使用，检测延迟比SeewoMonitorSystem还有多≥1.5s，经测试延迟0.6s-1.5s才会显示色块且并不显眼

/## Sound

/监控`media_capture.exe`并且播放声音来告知情况。
/当开始运行时，播放Windows硬件插入的系统提示音；当结束运行时，播放Windows硬件拔出的系统提示音。
/为了确保提示音能够播放，该程序会检测系统音量，如果系统音量低于设定阈值（默认`-10.5dB`），就先拉高系统音量到阈值再播放提示音，提示音播放完成再调回系统原音量；
/如果当前系统音量大于设定阈值，就不改变音量。
/但是，无论哪种情况，该程序都会解除静音并且不会恢复静音。

## RubbishCleaner

监控[原理](#原理)中提到的3个程序和上传网速，如果上传网速低持续1分钟，就尝试终止之前提到的3个程序。
监控程序不会主动结束进程，因此需要本程序

>[!IMPORTANT]
>此程序必须要能够成功调用`Nsudo.exe`才能发挥作用（建议加环境变量或者直接丢到System32里），因为需要提权。如果你有其他提权方案，自行更改此程序源码。
>有关`Nsudo.exe`，请参阅 [Nsudo](https://github.com/M2TeamArchived/NSudo)

# 使用本项目
## 打包成exe

1. 运行`pip.bat`安装依赖库
2. 使用`pyinstaller SeewoMonitor2.py -F -w`命令打包

> [!CAUTION]
> 使用`pyinstaller`打包的exe会被`WindowsDeferder`等报毒为特洛伊木马，程序本身并没有病毒
> 
> 要使用`RubbishCleaner.exe`，请注意`Nsudo.exe`

## 添加自启动（可选）

> [!IMPORTANT]
> 执行此操作前请确保`冰点还原`处于关闭状态，打开`explorer`，如果`C`盘是下图所示状态，则表明`C`盘被冻结，添加的自启动重启就会失效。
> 
> ![](docs/FreezedC.png)
> 
> 如果`冰点还原`是开启状态，你需要先关闭`冰点还原`。

具体如何添加自启动自行搜索引擎搜索。

# 局限性

[原理](#原理)中提到的3个程序有时候会自己启动，导致误报。不过1分钟后就会被[RubbishCleaner](#RubbishCleaner)清除。
目前尚不清楚相关机制。
