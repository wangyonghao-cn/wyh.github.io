---
title: flutter环境搭建
date: 2020-10-30 
---

## 系统要求

> windows7(64-bit) 

> 磁盘空间：400MB

> Git for Window

<!--more-->

## 获取SDK

> 可以去官网 https://flutter.io/sdk-archive/#windows 获取，因为众所周知的原因，可能需要翻墙，所以我们也可以
去gitHub中获取 https://github.com/flutter/flutter/releases，当然最简单的方法是下载android studio，它会自动
下载并配置

> 将安装包zip解压到你想安装Flutter SDK的路径

> 在Flutter安装目录的flutter文件下找到flutter_console.bat，双击运行并启动flutter命令行，接下来，你就可以在Flutter命令行运行flutter命令了

## 配置命令行

> 在用户变量下配置path，使用 ; 作为分隔符，然后将 flutter\bin的全路径作为它的值.

> 在“用户变量”下检查是否有名为”PUB_HOSTED_URL”和”FLUTTER_STORAGE_BASE_URL”的条目，如果没有，也添加它们。

```js
PUB_HOSTED_URL: https://pub.flutter-io.cn
FLUTTER_STORAGE_BASE_URL: https://storage.flutter-io.cn
```

## flutter doctor

> 该命令检查您的环境并在终端窗口中显示报告。Dart SDK已经在捆绑在Flutter里了，没有必要单独安装Dart。 仔细检查命令行输出以获取可能需要安装的其他软件或进一步需要执行的任务（以粗体显示）,第一次运行一个flutter命令（如flutter doctor）时，它会下载它自己的依赖项并自行编译。以后再运行就会快得多

## 配置VSCode

> 当以上步骤做完之后，就可以在Code的命令提示面板选择 Flutter: New Application Project,创建一个新的工程，当然会有提示没有设备连接，我们在使用android studio创建一个虚拟设备就OK了，下面是运行flutter后可能会使用到的命令：

```js
// Flutter run key commands.
r Hot reload.
R Hot restart.
h Repeat this help message.
d Detach (terminate "flutter run" but leave application running).
c Clear the screen
q Quit (terminate the application on the device).
```
