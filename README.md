<!-- markdownlint-disable MD033 MD041 -->

<div align="center">

# Maa-IDV

基于 [MaaFramework](https://github.com/MaaXYZ/MaaFramework) 开发的 第五人格黑杰克 半自动化助手。

</div>

## 简介
本项目通过模板匹配识别游戏界面，并自动执行进入黑杰克模式、准备、等待对局结束及返回主界面的 UI 操作，从而实现自动循环进入下一局。

由 [MaaFramework](https://github.com/MaaXYZ/MaaFramework) 强力驱动！
[MaaPracticeBoilerplate](https://github.com/MaaXYZ/MaaPracticeBoilerplate) 作为项目模板

## 功能介绍

当前脚本仅负责对局外的界面操作，不执行对局内角色操作、战斗决策或资源获取。

目前脚本可以自动执行以下流程：

1. 点击书本入口
2. 进入「混沌纷争」
3. 选择 黑杰克
4. 点击开始
5. 进入对局准备界面
6. 点击准备或开始按钮
7. 等待对局结束
8. 点击继续
9. 返回主界面
10. 自动重新开始下一轮

脚本会在部分节点识别失败时重复检测，直到对应按钮出现。

## 运行流程

任务节点的执行顺序如下：

```
clickbook
    ↓
混沌纷争
    ↓
BlackJack
    ↓
Start
    ↓
Battle
    ↓
ReadyStart
    ↓
Continue
    ↓
BackToMain
    ↓
clickbook
```

完成一轮后，脚本会重新进入活动并继续运行。

## 工作原理

本项目基于 MaaFramework 的 TemplateMatch 进行屏幕 UI 元素识别，并通过任务节点状态转换实现自动化流程。脚本不读取或修改游戏内部数据，主要通过识别屏幕上的 UI 元素来决定下一步操作。

脚本需要以下模板图片：

```
book.png
混沌纷争.png
BlackJack.png
Start.png
Ready.png
ReadyStart.png
Continue.png
BackToMain.png
```

请将模板图片放置在 MaaFramework 项目所使用的资源目录中。

## 分辨率说明

部分节点使用了固定识别区域，例如：

```
"roi": [
    1016,
    621,
    286,
    62
]
```

这表示只在画面的指定区域内寻找目标。

因此，不同分辨率、窗口大小或画面比例可能导致识别失败。建议使用制作模板图片时相同的分辨率运行。

当前包含固定识别区域的节点有：

BlackJack

ReadyStart

Continue

若脚本无法识别这些按钮，可以尝试：

1. 检查游戏窗口大小
2. 重新截取对应模板图片
3. 修改 roi 坐标
4. 适当降低或提高 threshold
5. 删除 roi，让脚本在整个画面中搜索

## 配置说明

匹配阈值

当前大部分节点使用：

```
"threshold": 0.7
```

数值越高，识别要求越严格。

经常识别不到：可以尝试降低至 0.65
经常误识别：可以尝试提高至 0.8
建议每次只调整少量数值并重新测试

## 等待时间

例如：

```
"post_delay": 10000
```

表示点击后等待 10000 毫秒，也就是 10 秒。

如果设备加载速度较慢，可以适当增加等待时间。

## 重复识别

部分节点的 next 中包含自身，例如：

```
"next": [
    "Start",
    "BlackJack"
]
```

这表示：

如果成功进入下一界面，则继续执行 Start
如果仍然停留在当前界面，则再次尝试识别 BlackJack

这样可以减少加载延迟造成的流程中断。

## 使用方法

1. 安装并配置 MaaFramework
2. 将本项目放入正确的资源目录
3. 准备所有模板图片
4. 启动游戏并进入主界面
5. 保持游戏窗口可见
6. 在 MaaFramework 中启动入口任务 clickbook

建议首次运行时观察完整流程，确认点击位置和模板识别结果正确。

## 常见问题

脚本一直停留在某个界面

可能原因：

模板图片与当前画面差异较大
游戏窗口分辨率不一致
threshold 设置过高
roi 坐标不适合当前分辨率
按钮被动画、弹窗或其他界面遮挡

可以查看 MaaFramework 日志，确认具体是哪个节点识别失败。

## 脚本点击了错误的位置

可以尝试：

重新截取更精确的模板图片
提高 threshold
为节点增加 roi
检查是否存在外观相似的按钮
检查模板图片是否包含过多背景

## 对局结束后没有继续

请检查：

Continue.png 是否与实际按钮一致
Continue 节点的 roi 是否正确
战斗时间是否超过当前等待时间
是否出现了额外的结算弹窗

## 脚本运行太快或太慢

可以修改：

```
"post_delay": 3000
```

以及：

```
"rate_limit": 3000
```

时间单位均为毫秒。

## 注意事项

1.本项目依赖图像识别，无法保证在所有设备和分辨率下正常工作。
2.游戏更新后，按钮样式变化可能导致模板失效。
3.请勿在运行过程中移动或缩放游戏窗口。
4.本项目仅提供基于屏幕图像识别的 UI 自动化操作，不包含修改游戏数据、内存修改、网络封包修改或绕过反作弊机制的功能。
5.请根据游戏规则及服务条款合理使用本项目，使用本项目造成的任何游戏账号或设备问题由使用者自行承担。

## 项目结构

示例：

```
.
├── README.md
├── LICENSE
├── assets
│   └── resource
│       ├── pipeline
│       │   └── task.json
│       └── image
│           ├── book.png
│           ├── 混沌纷争.png
│           ├── BlackJack.png
│           ├── Start.png
│           ├── Ready.png
│           ├── ReadyStart.png
│           ├── Continue.png
│           └── BackToMain.png
└── ...
```

实际目录结构请以你的 MaaFramework 项目配置为准。

## 鸣谢

本项目使用以下开源项目和模板：

MaaFramework
MaaPracticeBoilerplate

感谢 MaaXYZ 及相关贡献者提供的框架、项目模板和开发文档。
