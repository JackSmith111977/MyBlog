---
title: 使用conda管理python环境
date: 2025-12-15 21:45:00
tags: python
cover: https://kei-blog.oss-cn-beijing.aliyuncs.com/asset/115945063_p0-cut.jpg
categories: 
    - 编程语言
---

# 使用conda管理python环境

## 一. 安装conda
[官网下载](https://www.anaconda.com/docs/getting-started/miniconda/main)

1. 安装选项：

   * 点击 Next -> I Agree。
   * Select Installation Type: 建议选择 Just Me (recommended)（仅为当前用户安装），这样可以避免权限问题。
   * Choose Install Location: 保持默认即可，或者选一个没有中文和空格的路径。

2. 关键设置（Advanced Options）： 这一步最重要！
   * Create start menu shortcuts (supported packages only): 勾选。
   * Add Miniconda3 to my PATH environment variable: 强烈建议不要勾选。勾选后可能会干扰你系统里其他的软件。官方建议使用专门的 "Anaconda Prompt" 命令行工具，而不是系统的 CMD。
   * Register Miniconda3 as my default Python 3.x: 可以勾选（如果你希望它作为主力 Python）。
   * Clear the package cache upon completion: 不勾选。