---
title: 'How To Install GCC and Make on Windows 8 System'
layout: post
tags:
  - Compiler
category: Install
---

`GCC` and `Make` are built-in tools on linux machines.   However, as an user of Windows, I need to install them manually.

<!--more-->

## Step 1. Download and install MinGW Installation Manager

MinGW([Dowload](https://sourceforge.net/projects/mingw/)) 全稱 Minimalist GNU For Windows，是個精簡的Windows平台C/C++、ADA及Fortran編譯器，相比Cygwin而言，體積要小很多，使用較為方便。MinGW提供了一套完整的開源編譯工具集，以適合Windows平台應用開發，且不依賴任何第三方C運行時庫。

MinGW包括：
- 一套集成編譯器，包括C、C++、ADA語言和Fortran語言編譯器
- 用於生成Windows二進製文件的GNU工具的（編譯器、鏈接器和檔案管理器）
- 用於Windows平台安裝和部署MinGW和MSYS的命令行安裝器（mingw-get）
- 用於命令行安裝器的GUI打包器（mingw-get-inst）

## Step 2. Install `GCC` using MinGW Installation Manager

In the window of minGW, right click on `mingw32-gcc-g++` and select `Mark for Installation`.
![](https://i.imgur.com/oeL8TYt.png)

Then click `Installation` on the menu bar and select `Apply Change`. The installation of `gcc` would begin.

## Step 3. Install `Make` using MinGW Installation Manager

In the window of minGW, right click on `mingw32-gcc-g++` and select `Mark for Installation`.
![](https://i.imgur.com/zGGiGRe.png)


Then click `Installation` on the menu bar and select `Apply Change`. The installation of `gcc` would begin.

## References

- [Windows 安裝 Gcc 編譯器 - MinGW](http://blog.jex.tw/blog/2013/12/17/windows-install-gcc-compiler-mingw/)
- [SourceForge - MinGW](https://sourceforge.net/projects/mingw/)
- [StackOverflow - how to use gnu make on windows](http://stackoverflow.com/questions/12881854/how-to-use-gnu-make-on-windows)