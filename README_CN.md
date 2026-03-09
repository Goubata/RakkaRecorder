# RakkaRecorder

RakkaRecorder - Free PerfRecorder

JA   [https://github.com/Goubata/RakkaRecorder/blob/main/README.md](https://github.com/Goubata/RakkaRecorder/blob/main/README.md)

EN   [https://github.com/Goubata/RakkaRecorder/blob/main/README_EN.md](https://github.com/Goubata/RakkaRecorder/blob/main/README_EN.md)

一个免费的 Android 性能记录应用，可以测量并记录手机游戏或应用的性能。
可以同时查看 FPS、CPU/GPU 使用率、温度、以及电池功耗等信息。

支持 **Root / Shizuku** 两种方式运行。

> ⚠️ 源代码不公开，仅提供 APK 下载。

---

# 目录

* [这个应用是做什么的](#这个应用是做什么的)
* [运行环境要求](#运行环境要求)
* [使用方法](#使用方法)
* [可以测量的项目](#可以测量的项目)
* [FPS 测量原理](#fps-测量原理)
* [Jank / Stutter 检测](#jank--stutter-检测)
* [悬浮窗显示](#悬浮窗显示)
* [记录与图表](#记录与图表)
* [常见问题](#常见问题)
* [免责声明](#免责声明)

---

# 这个应用是做什么的

这是一个可以在游戏运行时实时显示 FPS、温度等信息，并且记录所有性能数据的应用。

开发这个应用的目标是：
**提供接近付费性能工具的功能，但完全免费。**

主要功能：

* 游戏运行时悬浮显示 FPS
* 实时监控 CPU / GPU / RAM / 电池
* 按 session 保存测量数据到数据库
* 使用图表查看历史数据（横轴为测量时间）
* 自动统计 Jank / Stutter / Frame Time 稳定度
* 支持 CSV 导出用于外部分析
* 支持 **中文 / English / 日本語** 三种语言

---

# 运行环境要求

| 项目 | 内容                      |
| -- | ----------------------- |
| 系统 | Android 14 以上 (API 34+) |
| 权限 | Root **或** Shizuku      |
| 其他 | 悬浮窗权限、使用情况访问权限          |

必须使用 **Root 或 Shizuku** 其中之一。
否则 FPS 等部分数据无法正确获取。

> Shizuku 使用方法请参考
> [https://shizuku.rikka.app/](https://shizuku.rikka.app/)

---

# 使用方法

## 首次启动

第一次启动应用时会显示 **Setup 对话框**。

* **Shizuku 已运行** → 显示「允许 Shizuku 权限」按钮，点击授权
* **Root 可用** → 自动检测并显示「✓ 可用」，点击 OK 即可
* **两者都没有** → 显示错误提示，请先安装并启动 Shizuku
* 点击「稍后」可以跳过设置

---

## 基本流程

1. 打开应用
2. 在主页确认权限状态
3. 在「测量目标应用」中选择要测试的应用（可选）
4. 点击 **开始测量**
5. 打开需要测试的应用或游戏
6. 使用结束后回到 RakkaRecorder
7. 点击 **停止并保存**
8. 在 **记录页面** 查看结果

---

## 如果选择了目标应用

当指定应用被打开时：

* 测量会 **自动开始**
* 关闭应用时 **自动停止并保存**

即使返回桌面或切换应用，也不会中断记录。

> 目标应用 **只能选择一个**。

---

## 未选择应用（全局测量模式）

点击 **开始测量** 后：

* 应用会持续记录数据
* 当前前台应用变化时
  FPS 监控目标会自动切换

---

# 可以测量的项目

## FPS

* FPS
* 总帧数
* 平均 / 最小 / 99百分位 / 95百分位

---

## CPU

* CPU 总使用率
* 每个核心使用率
* 各 Cluster 频率（Little / Big / Prime）
* CPU 温度

---

## GPU

* GPU 使用率
* GPU 频率（支持 Snapdragon / MediaTek）
* GPU 温度

---

## 内存

* RAM 使用量（已用 / 总量 / 空闲 / Cache）

---

## 电池与功耗

* 电池电流 (mA)
* 电池电压 (mV)
* 电池电量 (%)
* 电池温度
* 功耗 (mW)
* 每帧功耗 (mW/fps)

---

## 其他

* 机身表面温度 (Skin Temperature)

---

# FPS 测量原理

FPS 测量采用 **四级回退机制**：

```
方式A: SurfaceFlinger --timestats (totalFrames差分)
  → Android 14上精度最高

方式B: dumpsys gfxinfo PROFILEDATA
  → 适用于普通 HWUI 应用

方式C: SurfaceFlinger --latency
  → 用于旧游戏或未使用 BLAST 的 layer

方式D: gfxinfo Total frames差分
  → 最后备用方案
```

只要 **方式A** 成功，就不会使用其他方式。

为了避免屏幕静止时 FPS 下降导致的异常尖峰，
内部实现了 **缓冲区污染防护**。

FPS 会自动限制在 **屏幕刷新率以内**。

例如：

| 刷新率   | 最大FPS  |
| ----- | ------ |
| 60Hz  | 60fps  |
| 120Hz | 120fps |

---

# Jank / Stutter 检测

用于识别：

**平均 FPS 很高但体感卡顿的情况。**

结果会显示在：

* **概要页**
* **FPS 页**

---

## Jank 率（掉帧指标）

当 FPS 低于 **刷新率的 85%** 时记为 Jank 秒。

例如：

60Hz 设备
fps < 51

| 数值     | 评价   |
| ------ | ---- |
| 0–10%  | 良好   |
| 10–20% | 略不稳定 |
| 20%+   | 存在问题 |

---

## Stutter 率（抖动指标）

连续样本 FPS 变化超过 **5fps** 的比例。

| 数值     | 评价   |
| ------ | ---- |
| 0–15%  | 良好   |
| 15–30% | 略不稳定 |
| 30%+   | 存在问题 |

---

## Frame Time σ（标准差）

通过

```
1000 ÷ FPS
```

估算帧时间(ms)并计算标准差。

越接近 **0** 表示帧间隔越稳定。

> 注意：
> 所有计算基于 **1秒FPS样本**，
> 不是逐帧精确测量。

---

# 悬浮窗显示

可以在设置中选择要显示的项目：

* FPS
* CPU 使用率 / 核心 / 频率 / 温度
* GPU 使用率 / 频率 / 温度
* RAM 使用情况
* 电池电流 / 电压 / 温度
* 功耗 / 每帧功耗
* 机身温度
* FPS 调试信息

支持：

* **9 个位置**
* 字体大小
* 背景透明度

支持：

* **Dark Mode**
* **Material You (Android 12+)**

---

# 记录与图表

## 记录列表

每个测量 Session 会显示：

* 应用名称
* 时间
* 持续时间
* 样本数量

---

## 详情页面

| 标签      | 内容                                     |
| ------- | -------------------------------------- |
| 概要      | FPS / CPU / RAM / 功耗平均值 + Jank/Stutter |
| FPS     | avg / min / 99th / 95th / 总帧数 + 图表     |
| CPU     | 使用率 / 温度                               |
| Cluster | 各 CPU cluster 频率                       |
| GPU     | 使用率 / 频率 / 温度                          |
| RAM     | 内存                                     |
| 功耗      | 功率 / 电流 / 电压                           |
| 温度      | CPU / GPU / Skin                       |
| 电池      | 电量 / 电流                                |

图表显示：

* min
* avg
* max

横轴：

**测量时间**

---

## CSV 导出

可以导出 CSV 文件。

格式：

```
1行 = 1秒数据
```

包含 **所有监控指标**。

---

# 常见问题

**Q：没有 Root 可以使用吗？**
A：可以，只要使用 Shizuku。

---

**Q：FPS 一直是 0**
A：请确认 Root 或 Shizuku 权限。

需要执行 `dumpsys` 命令的权限。

---

**Q：功耗数据不正确**
A：计算方式：

```
|I(mA)| × V(mV) ÷ 1,000,000 = W
```

部分设备电流单位为 **μA**，
因此可能存在偏差。

---

**Q：支持 Snapdragon 和 MediaTek 吗？**
A：支持。

应用会自动检测 SoC 并切换 GPU / CPU 读取路径。

MediaTek (Dimensity / Helio)
会自动尝试多个 GPU 频率节点。

---

**Q：可以选择多个测试应用吗？**
A：不可以，目前只支持一个。

---

**Q：Jank / Stutter 的准确性如何？**
A：基于 **1秒FPS采样的近似值**。
仅供参考，不等同于逐帧分析。

---

# 免责声明

* 本应用为个人兴趣开发
* 使用 Root / Shizuku 存在风险
* 使用需自行承担责任
* 对设备损坏或数据丢失不承担责任
* 源代码未公开
* 禁止修改或重新分发 APK

---

# Copyright

Copyright © 2026 Rakkashin.
All Rights Reserved.

The source code is private.
Modification, redistribution, or reverse engineering of the APK is prohibited.
