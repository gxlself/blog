---
title: Paste — 轻量级隐私优先的 macOS 剪贴板管理器
date: 2026-03-18 10:00:00
categories:
  - 工具推荐
tags:
  - macOS
  - 剪贴板
  - 效率工具
  - 开源
---

## 简介

Paste 是一款轻量级、隐私优先的 macOS 剪贴板管理器。它能够记录你的剪贴板历史，支持全文搜索、Paste Stack、Pinboards、自定义分类以及可选的 iCloud 同步功能。

![](https://paste.gxlself.com/images/hero.png)

## 为什么选择 Paste？

在日常工作开发中，我们经常需要复制粘贴各种内容：代码片段、文档内容、链接、图片等。系统自带的剪贴板只能保存最近一次复制的内容，这大大降低了工作效率。

Paste 解决了这个问题，它可以：

- **记录剪贴板历史** - 不再担心复制后被覆盖
- **快速搜索** - 全文搜索快速找到需要的内容
- **分类管理** - 文本、图片、文件、链接自动分类
- **Pinboards** - 将常用内容固定，随时调用
- **Paste Stack** - 依次粘贴多个剪贴项

## 核心功能

### 剪贴板监控

- 约 300ms 轮询间隔，捕获纯文本、富文本（RTF）、图片和文件路径
- 智能去重：基于 SHA-256 内容哈希，重复内容仅刷新时间戳
- 自写入过滤：Paste 写入的内容不会立即被重新记录
- 排除应用：可配置排除密码管理器等敏感应用

### 主面板

- 半透明浮层面板，键盘优先设计
- 支持底部/顶部/左侧/右侧位置设置
- 自适应卡片布局，滑入动画效果
- 辅助粘贴：配合辅助功能权限，自动粘贴到当前应用

### 搜索与过滤

- 实时全文搜索，输入即搜
- 类型过滤：全部、文本、图片、文件、链接
- 正则表达式预设过滤器
- 自定义分类（标签）

### Pinboards 与 Paste Stack

| 功能 | 说明 |
|------|------|
| Pinboards | 多个固定槽位（默认 5 个，最多 10 个），全局快捷键切换 |
| Paste Stack | 将剪贴项加入队列，按顺序粘贴 |
| 纯文本粘贴 | 默认纯文本，或按住 ⇧ 粘贴纯文本 |

### 快捷键

| 动作 | 快捷键 |
|------|--------|
| 打开/关闭面板 | ⌘⇧V |
| 打开 Paste Stack | ⌘⇧C |
| 下一个 Pinboard | ⌘] |
| 上一个 Pinboard | ⌘[ |
| 快速粘贴 1-9 | ⌘1 ~ ⌘9 |

## 跨平台支持

Paste 不仅支持 macOS，还提供了 iOS 版本：

- **macOS 应用** - 菜单栏应用，记录剪贴板历史
- **iOS 应用** - 浏览相同的历史记录、Pinboards
- **iOS 自定义键盘** - 在任意应用中插入保存的剪贴项

当你在 macOS 和 iOS 上都开启 iCloud 同步时，历史记录可以在设备间同步。

## 隐私保护

Paste 非常注重用户隐私：

- 数据**本地存储**，iCloud 同步是可选的
- **无分析**、无崩溃报告、无第三方 SDK
- 敏感应用可通过排除规则跳过记录

## 安装方式

### macOS

1. 访问 [GitHub Releases](https://github.com/gxlself/Paste/releases) 下载最新的 `Paste-*-macos.zip`
2. 解压后将 Paste 拖入应用程序文件夹
3. 首次运行时在「系统设置 → 隐私与安全性 → 辅助功能」中授权

### iOS

- [App Store 下载](https://apps.apple.com/us/app/paste-g/id6758338373)（$2.99）
- 或通过 [TestFlight](https://testflight.apple.com/join/2UKC5P27) 参与测试

## 开源

Paste 是开源项目，代码托管在 GitHub：

- **仓库地址**：[github.com/gxlself/Paste](https://github.com/gxlself/Paste)
- **许可证**：MIT License

如果你觉得 Paste 有用，欢迎在 GitHub 上给个 Star ⭐

## 相关链接

- **官网**：[paste.gxlself.com](https://paste.gxlself.com)
- **GitHub**：[github.com/gxlself/Paste](https://github.com/gxlself/Paste)
- **问题反馈**：[GitHub Issues](https://github.com/gxlself/paste/issues)

---

如果这款工具对你有帮助，欢迎支持开发者的持续维护工作！

