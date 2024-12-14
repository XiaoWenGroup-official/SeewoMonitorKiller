# SeewoMonitorKiller
> [!IMPORTANT]
> 本项目(SeewoMonitorKiller)计划基于GPL-v3协议开源
> 目前仅上传了打包成品版本，开源计划有序推进中，请稍安勿躁
> ##111云盘--高速云存储（本作者云盘）
> https://111pan.cn/ 欢迎使用，您可以在本云盘中购买会员以打赏本项目，感谢支持

SeewoMonitorKiller 是针对希沃希沃管家监控的反监控系统 最新版本V1.4（已发布）

由于班主任时不时通过希沃的巡课系统（俗称监控）来查看上课情况，这对学生来说当然不是一个好消息。因此，本反监控系统就是监控希沃，并在有异常情况时立即报告。

> [!CAUTION]
>
> 本项目最后更新于2024年12月14日，希沃管家相关行为机制可能已经更改，请在安全环境自行测试项目是否任然有效，有相关问题欢迎issue
>
> 本项目基于 “SeewoMonitorSystem” 原项目地址 https://github.com/DengHanxu/SeewoMonitorSystem
>
> 如您使用本软件，则代表你以阅读并同意：因使用本软件而产生的任何后果作者均不承担这一声明

> [!WARNING]
>
> 本项目的思路构造以及极小部分源码借鉴SeewoMonitorSystem，由于GPL-v3协议有继承性，现计划基于GPL-v3开源

# 原理

希沃巡课系统主要依赖3个程序，`media_capture.exe`，`screenCapture.exe`，`rtcRemoteDesktop.exe`，而`media_capture.exe`就是负责获取摄像头数据的。
因此，不断查询当前进程列表并检查是否有`media_capture`就能大致确定（[这种方法并不完全准确](#局限性)）是否有老师正在看监控。

# 主要构成
- [SeewoMonitorKiller](##SeewoMonitorKiller)

## SeewoMonitorKiller

SeewoMonitorKiller 整合 SeewoMonitorSystem 的部分代码及在此基础上增加部分功能，将负责监控进程及杀进程的功能以指示块和系统通知的形式显示

##功能介绍

###特性1
SeewoMonitorKiller (V1.2至最新) 在检测到[原理](#原理)中的三个应用程序进程存在时会进行系统通知(仅兼容Windows10&Windows11)，期间会调用程序根目录下的图标文件作为通知图标，如图![](notice-1.png)，确保根目录下有white.ico（系统黑暗模式使用）和black.ico（系统白天模式使用）。同时顶部会出现和 [SeewoMonitorSystem](https://github.com/DengHanxu/SeewoMonitorSystem) 一样的四个4x4图标，但是最左端的绿色图标机制已更改为右侧无任何进程存在的情况下亮起，便于在快速查看进程是否运行或本程序是否运行正常。

###特性2
SeewoMonitorKiller（V1.4至最新）将在托盘放置一个Windows Defender的轮廓勾线图标，该图标仅在监控程序均未运行时显示。当被监控程序三个之中至少有一个运行时将会将图标换为Windoows Defender的填充图标（内部为黑色），比顶部栏更显眼，方便观察。

###特性3
快捷键Alt+Shift+X打开关于页面可设置相关内容（V1.1-最新）
快捷键Alt+Shift+C快速退出本程序（V1.2-最新）

##版本介绍

### SeewoMonitorKiller-V1.0
这是第一个版本，默认以大约`1次/秒`的速度检测`media_capture.exe`，`screenCapture.exe`，`rtcRemoteDesktop.exe`。
当检测到`media_capture.exe`或`screenCapture.exe`或`rtcRemoteDesktop.exe`在运行时，就认为监控系统正在运行，并在屏幕上方正中的位置(已修复SeewoMonitorSystem显示偏左的问题)显示一个4x4像素的红/橙/蓝色方块。
当`media_capture.exe`或`screenCapture.exe`或`rtcRemoteDesktop.exe`结束运行时，就认为本次监控结束，对应方块消失。
> [!IMPORTANT]
> 当前版本已不建议使用，故没有上传，检测延迟比 [SeewoMonitorSystem](https://github.com/DengHanxu/SeewoMonitorSystem) 还有多≥1.5s，经测试延迟0.6s-1.5s才会显示色块且并不显眼

>[!IMPORTANT]
>此程序已在代码中修改提权方式，不必像 [SeewoMonitorSystem](https://github.com/DengHanxu/SeewoMonitorSystem) 一样调用`Nsudo.exe`才能发挥作用
>有关`Nsudo.exe`，请参阅 [Nsudo](https://github.com/M2TeamArchived/NSudo)

### SeewoMonitorKiller-V1.1
这是第二个版本，本版本增加了检测进程时间、低网络检测时间、低网络关闭阈值、被监控程序被本程序关闭(低网络情况下)的时间的自定义，增加了是否同意持续低网络(用户阈值网络使用大小)时间到用户阈值时间是否杀掉程序的自定义选项
> [!IMPORTANT]
> 当前版本已不建议使用，故没有上传

> [!IMPORTANT]
> 当前版本与V1.0版本在初始化时均要使用3s以上且顶部绿灯在检测到右侧程序运行1次后再也不会亮起(暂未确定原因，故后来版本-在V1.2及之后的版本修改了绿灯的逻辑)

### SeewoMonitorKiller-V1.2
这是第三个版本，大版本更新，修复了低网络时间检测无法生效的问题，并优化通知方案，降低监测误差，增加“关闭通知”选项用于关闭系统通知，增加错误处理机制

>[!important]
>在该版本中关闭通知无法生效

### SeewoMonitorKiller-V1.3
这是第四个版本，更换数组使关于中自定义监测时间可为小数，进一步降低监测延迟，优化其它方面使代码容错率更高，将“关闭通知”改为“静音通知”

>[!important]
>静音通知仅为预期目标，实践证明无法实现除非使用plyer模块通知（本版本仍使用win10toast模块）

### SeewoMonitorKier-V1.4
这是第五个版本，大版本更新，改回“关闭通知”，修复每个通知都会在任务栏生成一个图标的问题，增加本程序任务栏图标显示，右键可打开“关于”或退出。本版本还优化了关闭程序方案，使本程序在使用快捷键关闭或右键图标关闭能够正常关闭该程序。同时增加被监控程序的强制关闭方案，用两个方案同时在用户已设置的情况下关闭被监控程序（无需提权）。降低关于打开时间。

# 使用本项目
## 打包成exe

1. 运行`pip.bat`安装依赖库
2. 使用`pyinstaller --onefile --noconsole --add-data "white.ico;.\\white.ico" --add-data "black.ico;.\\black.ico" --hidden-import=plyer.platforms.win.notification SeewoMonitorKiller-V版本号(例如V1.2).py`命令打包
> [!important]
> 可选用64位打包pyinstaller打包代码如下（以1.4版本举例）
> pyinstaller --onefile --noconsole --add-data "white.ico;." --add-data "black.ico;." --add-data "tz.ico;." --hidden-import=plyer.platforms.win.notification --target-architecture amd64 SeewoMonitorKiller-V1.4.py

> [!CAUTION]
> 使用`pyinstaller`打包的exe会被`WindowsDefender`等报毒为特洛伊木马，程序本身并没有病毒
> 现已知会被Defender、江民杀毒、瑞星杀毒报毒（V1.4）

## 添加自启动（可选）

> [!IMPORTANT]
> 执行此操作前请确保`冰点还原`处于关闭状态，打开`explorer`，如果`C`盘所示状态有一个盾形图标，则表明`C`盘被冻结，添加的自启动重启就会失效。
> 
> 如果`冰点还原`是开启状态，你需要先关闭`冰点还原`。

具体如何添加自启动自行搜索引擎搜索。

# 局限性

[原理](#原理)中提到的3个程序有时候会自己启动，导致误报。不过在用户于关于中设置的阈值时间后被清除。
目前尚不清楚相关机制。

>[!important]
>当前（截止V1.4）还未测试关于网络计量的相关逻辑是否能够正常运行，故推荐在关于中关闭“允许杀进程”选项以防万一
