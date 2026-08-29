# PotPlayer 常用功能与快捷键

> [!info] 简介  
> **PotPlayer** 是 Windows 上功能非常丰富的本地音视频播放器。除了基本播放外，它还提供字幕管理、音视频轨道切换、倍速播放、A-B 循环、书签、截图、画面调整、音频处理、硬件解码、视频信息查看等功能。官方页面也将硬件加速、多格式字幕、3D/360°播放、OpenCodec 等列为主要功能。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))
> 
> **注意：** PotPlayer 支持自定义快捷键，因此不同版本或个人配置可能存在差异。以下以常见默认快捷键为主。

---

# 1. 基本播放

## 1.1 播放 / 暂停

```text
Space
```

播放和暂停视频。

这是使用频率最高的快捷键。

---

## 1.2 前进 / 后退

| 快捷键              | 功能                 |
| ---------------- | ------------------ |
| `←`              | 后退约 5 秒            |
| `→`              | 前进约 5 秒            |
| `Ctrl + ←`       | 后退约 30 秒           |
| `Ctrl + →`       | 前进约 30 秒           |
| `Shift + ←`      | 后退约 1 分钟           |
| `Shift + →`      | 前进约 1 分钟           |
| `Ctrl + Alt + ←` | 后退约 5 分钟           |
| `Ctrl + Alt + →` | 前进约 5 分钟           |
| `G`              | 跳转到指定时间 / 帧        |
| `PageDown`       | 播放**下一个视频**（下一个文件） |
| `PageUp`         | 播放**上一个视频**（上一个文件） |

PotPlayer 很适合看课程，因为可以通过不同级别的快进、快退快速定位内容。 ([PotPlayer](https://potplayer.multimediaplayer.net/tutorials/shortcuts/?utm_source=chatgpt.com "PotPlayer Keyboard Shortcuts - PotPlayer tutorials website"))

---

# 2. 倍速播放

|快捷键|功能|
|---|---|
|`Z`|恢复正常 / 上次速度|
|`X`|降低播放速度|
|`C`|提高播放速度|

例如：

```text
1.0x
 ↓ C
1.1x
 ↓ C
1.2x
 ↓ C
1.3x
```

看课程时非常实用。

### 推荐用法

```text
正常内容 → 1.5x
熟悉内容 → 2.0x
复杂内容 → 0.8x
听不清楚 → 0.5x
```

---

# 3. 全屏与窗口

|操作|快捷键|
|---|---|
|全屏 / 窗口|`Enter`|
|退出全屏|`Esc`|

全屏观看视频时，可以直接使用：

```text
Enter
```

在窗口和全屏之间切换。

---

# 4. 音量与声音

## 4.1 音量

|快捷键|功能|
|---|---|
|`↑`|增大音量|
|`↓`|减小音量|
|`M`|静音 / 取消静音|

---

## 4.2 多音轨

如果一个视频包含多个音频轨道，例如：

```text
Audio 1 → 中文
Audio 2 → English
Audio 3 → Japanese
```

可以通过右键菜单中的：

```text
音频 → 音频轨道
```

进行切换。

PotPlayer 支持多音轨以及不同音频输出设备的选择。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

# 5. 字幕

PotPlayer 的字幕功能是它非常重要的一部分。

支持包括：

```text
SRT
ASS
SSA
SMI
VobSub
Blu-ray 字幕
```

官方说明也明确列出了 SMI、SRT、VobSub、Blu-ray、ASS/SSA 等字幕支持。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

## 5.1 加载字幕

常见操作：

```text
右键
  ↓
字幕
  ↓
加载字幕
```

也可以：

```text
Alt + O
```

加载外部字幕。

---

## 5.2 自动加载字幕

如果视频：

```text
example.mkv
```

字幕：

```text
example.srt
```

放在同一个目录下，通常可以自动匹配加载。

```text
📁 Video
├── example.mkv
└── example.srt
```

---

## 5.3 显示 / 隐藏字幕

```text
Alt + H
```

---

## 5.4 字幕同步

这是看网络视频、课程、电影时非常有用的功能。

如果出现：

```text
人物说话
    ↓
字幕晚 2 秒出现
```

可以调整字幕同步时间。

常见快捷键：

```text
,     字幕提前/延后小幅调整
Ctrl + ,     大幅调整
.     反方向调整
Ctrl + .     反方向大幅调整
```

也可以通过：

```text
右键
→ 字幕
→ 字幕同步
```

进行调整。

PotPlayer 支持字幕同步、字体、大小、位置等设置。 ([potplayer.dev](https://potplayer.dev/guides.html?utm_source=chatgpt.com "PotPlayer Guides | Troubleshooting, HEVC, Subtitles, Antivirus"))

---

# 6. 字幕样式

可以调整：

```text
字体
字体大小
字体颜色
描边
阴影
位置
透明度
字符集
```

入口通常为：

```text
F5
→ 字幕
→ 字体样式
```

例如中文字幕乱码，可以检查：

```text
字幕
→ 字体样式
→ 字符集
```

选择：

```text
中文（简体）
```

或者：

```text
自动选择
```

---

# 7. 双字幕

PotPlayer 可以同时显示多个字幕轨道。

例如：

```text
English
The operating system manages hardware resources.

中文
操作系统管理硬件资源。
```

这对于：

- 英语学习
    
- 技术视频
    
- 外语课程
    

非常有用。

可以通过：

```text
字幕
→ 字幕轨道
```

选择不同字幕。

---

# 8. 画面比例与缩放

PotPlayer 可以调整：

```text
视频比例
画面大小
缩放
裁剪
位置
旋转
```

右键视频：

```text
视频
→ 视频比例
```

常见比例：

```text
自动
原始比例
16:9
4:3
自定义比例
```

---

# 9. 画面旋转

遇到手机竖屏视频或者方向错误的视频，可以使用：

```text
视频
→ 图像处理
→ 旋转
```

例如：

```text
90°
180°
270°
```

---

# 10. 镜像 / 翻转

PotPlayer 可以对视频进行：

```text
水平翻转
垂直翻转
```

例如：

```text
普通视频
   ↓
水平镜像
   ↓
左右反转
```

这个功能在看：

- 摄像头录像
    
- 自拍视频
    
- 实验视频
    
- 教学视频
    

时比较有用。

---

# 11. 截图

PotPlayer 可以直接截取当前视频画面。

常用快捷键：

```text
Ctrl + E
```

截图相关功能也可以通过：

```text
右键
→ 视频
→ 视频录制
```

进入。

官方功能介绍也明确支持按场景截图。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

## 截图的常见用途

```text
课程 PPT
代码
数学公式
电路图
实验步骤
游戏画面
电影画面
```

如果你用 PotPlayer 看技术课程，这个功能非常值得使用。

---

# 12. A-B 循环

这是 **学习视频最值得掌握的功能之一**。

例如：

```text
10:20 ───────────── 10:45
       ↑             ↑
       A             B
```

只循环播放：

```text
10:20 ~ 10:45
```

可以用于：

- 重复听一句英语
    
- 重复看一个知识点
    
- 分析动作
    
- 学习代码讲解
    
- 听写
    
- 学习歌曲
    

PotPlayer 支持设置 A/B 区间并重复播放。 ([PotPlayer](https://potplayer.multimediaplayer.net/tutorials/shortcuts/?utm_source=chatgpt.com "PotPlayer Keyboard Shortcuts - PotPlayer tutorials website"))

---

# 13. 书签 / 标记

如果一个视频很长，例如：

```text
2 小时课程
```

可以给重要位置添加书签。

例如：

```text
00:15:20  指针
00:32:10  内存管理
00:58:40  虚拟内存
01:24:15  页表
```

常见操作：

```text
P
```

添加书签。

```text
H
```

进入章节 / 书签相关功能。

官方也将书签和章节定位列为功能之一。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

# 14. 播放列表

快捷键：

```text
F6
```

打开播放列表。

适合一次观看：

```text
第01课.mp4
第02课.mp4
第03课.mp4
第04课.mp4
```

可以直接批量加入播放列表。

---

# 15. 打开文件 / 文件夹

常用：

```text
F2 → 打开文件夹
F3 → 打开文件
```

也可以直接把：

```text
视频文件
文件夹
字幕文件
```

拖入 PotPlayer。

---

# 16. 打开 URL

```text
Ctrl + U
```

可以打开网络 URL。

例如某些支持直接播放的：

```text
HTTP
网络媒体
直播流
```

PotPlayer 也支持网络流媒体、设备以及其他输入源。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

# 17. 视频信息

播放过程中按：

```text
Tab
```

或者使用播放信息相关功能，可以查看当前视频的各种信息。

典型信息包括：

```text
分辨率
帧率
视频编码
音频编码
码率
CPU 占用
GPU 解码
```

例如：

```text
Video
────────────────
1920 × 1080
60 FPS
H.264
8 Mbps

Audio
────────────────
AAC
48 kHz
2 Channels
```

这个功能对于**排查视频卡顿、编码兼容性、硬件解码是否生效**非常有用。PotPlayer 也提供播放过程中查看编解码器、码率和 CPU/GPU 状态的能力。 ([potplayer.dev](https://potplayer.dev/features.html?utm_source=chatgpt.com "PotPlayer Features | Codecs, Hardware Acceleration, 3D, Subtitles"))

---

# 18. 硬件解码

对于：

```text
4K
8K
HEVC / H.265
AV1
高码率视频
```

硬件解码非常重要。

PotPlayer 支持：

```text
DXVA
CUDA
QuickSync
```

官方页面明确将 DXVA、CUDA、QuickSync 列为硬件加速方式。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

## 18.1 查看硬件解码

进入：

```text
F5
→ 滤镜
→ 视频解码器
```

检查：

```text
内置视频解码器
```

以及对应的硬件加速选项。

---

## 18.2 为什么需要硬件解码

例如播放：

```text
4K HEVC
```

软件解码：

```text
CPU ████████████████ 90%
GPU ██
```

硬件解码：

```text
CPU ███
GPU ████████
```

通常可以明显降低 CPU 负载。

---

# 19. 音频均衡器

PotPlayer 自带音频处理功能。

可以调整：

```text
低音
中音
高音
音量
均衡器
声道
音频输出
```

适合：

```text
耳机
音箱
电影
音乐
人声增强
```

---

# 20. 音频延迟

如果视频出现：

```text
人物嘴巴动了
      ↓
声音稍后才出来
```

可以调整：

```text
音频同步 / 音频延迟
```

这个功能对于：

- 蓝牙耳机
    
- 网络视频
    
- 录屏视频
    
- 编码异常的视频
    

特别有用。

---

# 21. 视频滤镜

PotPlayer 可以进行各种视频处理，例如：

```text
锐化
降噪
模糊
颜色调整
亮度
对比度
饱和度
旋转
裁剪
```

入口：

```text
F5
→ 视频
```

或者播放时：

```text
右键
→ 视频
```

---

# 22. HDR / 高分辨率视频

对于：

```text
HDR
4K
8K
10-bit
HEVC
AV1
```

需要关注：

```text
解码器
→ 硬件加速
→ 视频渲染器
→ 色彩空间
```

尤其是高分辨率视频出现：

```text
卡顿
黑屏
颜色异常
CPU 占用过高
```

时，可以先按：

```text
Tab
```

查看当前解码状态。

---

# 23. OpenCodec

如果遇到某个视频：

```text
无法播放
没有声音
画面异常
某种特殊编码无法解码
```

可以考虑：

```text
OpenCodec
```

PotPlayer 支持通过 OpenCodec 增加额外的编解码能力。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

不过一般情况下：

> **不要为了“全能”而随便安装大量第三方 Codec Pack。**

优先使用 PotPlayer 自带解码器。

---

# 24. 3D / 360° 视频

PotPlayer 还支持：

```text
3D
Side by Side
Top and Bottom
Page Flipping
360° 全景视频
```

例如：

```text
3D SBS

┌─────────┬─────────┐
│ 左眼画面 │ 右眼画面 │
└─────────┴─────────┘
```

可以在视频相关菜单中选择对应的 3D 模式。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

# 25. 播放设备

PotPlayer 不仅可以播放普通文件，还可以处理一些设备和输入源，例如：

```text
DVD
TV
HDTV
摄像头
```

官方功能介绍中也列出了 DVD、TV、HDTV 等设备支持。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))

---

# 26. 常用设置入口

PotPlayer 最重要的设置入口：

```text
F5
```

进入：

> **偏好设置**

这里基本可以把 PotPlayer 的所有核心功能配置一遍。

常见设置分类：

```text
常规
播放
字幕
视频
音频
滤镜
网络
播放列表
控制
鼠标
键盘
```

---

# 27. 推荐掌握的快捷键

如果不想记一百多个快捷键，实际上掌握下面这些就够用了：

|快捷键|功能|
|---|---|
|`Space`|播放 / 暂停|
|`Enter`|全屏|
|`← / →`|前后 5 秒|
|`Ctrl + ← / →`|前后 30 秒|
|`Shift + ← / →`|前后 1 分钟|
|`G`|跳转时间|
|`X`|减速|
|`C`|加速|
|`Z`|恢复正常速度|
|`↑ / ↓`|音量|
|`M`|静音|
|`Alt + H`|显示 / 隐藏字幕|
|`Alt + O`|加载字幕|
|`P`|添加书签|
|`F6`|播放列表|
|`F5`|偏好设置|
|`Tab`|播放信息|
|`Ctrl + E`|截图|
|`Ctrl + U`|打开 URL|

---

# 28. 学习视频的推荐工作流

如果主要使用 PotPlayer **看课程、技术视频、英语视频**，可以建立下面这套习惯：

```text
                打开课程
                   │
                   ▼
              正常速度播放
                   │
          ┌────────┴────────┐
          ▼                 ▼
       内容简单           内容复杂
          │                 │
        1.5x~2x           0.8x~1x
                            │
                            ▼
                       Space 暂停
                            │
                     ← / → 精确定位
                            │
                            ▼
                       A-B 循环
                            │
                            ▼
                         理解
                            │
                            ▼
                        P 加书签
                            │
                            ▼
                      截图 / 做笔记
```

特别推荐：

```text
倍速 + A-B循环 + 书签 + 截图 + 字幕同步
```

这几个功能组合起来，PotPlayer 就不只是一个“播放器”，而是一个相当好用的**课程/视频学习工具**。

---

# 29. Obsidian 学习笔记推荐

如果你准备把 PotPlayer 作为自己的常用工具，可以重点记下面这一张：

> [!tip] PotPlayer 五大核心功能

```text
① 播放控制
   Space / ← → / G / X C Z

② 字幕
   加载 / 切换 / 同步 / 样式 / 双字幕

③ 学习
   A-B 循环 / 书签 / 倍速 / 截图

④ 视频处理
   比例 / 缩放 / 裁剪 / 旋转 / 镜像 / 滤镜

⑤ 性能
   硬件解码 / 编解码器 / 视频渲染 / 播放信息
```

> [!important] 核心思路  
> **日常使用不要去背 PotPlayer 的全部功能。**
> 
> 真正常用的是：
> 
> **播放 → 定位 → 倍速 → 字幕 → 循环 → 书签 → 截图 → 查看解码状态**
> 
> 其余功能在遇到具体需求时再用即可。

官方目前仍支持 Windows 10/11，并持续更新；硬件加速、字幕、书签、OpenCodec 等属于其核心功能。 ([Potplayer](https://potplayer.tv/?lang=zh_CN&utm_source=chatgpt.com "Global Potplayer"))