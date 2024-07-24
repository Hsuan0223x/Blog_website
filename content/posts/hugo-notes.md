+++
title = 'hugo框架 + GitHub Actions + Cloudflare Pages'
date = 2024-07-13T18:00:37+08:00
draft = false
tags =["hugo","website","notes"]
+++


## 前言
~~這個貼文原本只是測試貼文而已~~

一開始只是想說，要用網站來記錄心得，然後看到很多人利用 Hugo or Hexo 來建立網站

原本是想要用Hexo來架，但自己實作下來對於Hugo的主題比較喜歡

然後在網路上有很多大神的Hugo介紹，然後有Hugo官方文件可以參考(幾乎都是英文)

如果想要興建跟我一樣的主題，也可以直接利用Blowfish主題提供的工具來慢慢建立(互動式)

## 建立網站

我是先利用Hugo建好網站後，再安裝Blowfish主題

```
hugo new site [site-name]
cd [site-name]
git init
git submodule add -b main https://github.com/nunocoracao/blowfish.git themes/blowfish
```
然後要記得把Hugo原本的config.toml檔案給換成Blowfish Theme提供的config檔案

這樣去查找Blowfish的文件時才準確

## 文檔

[Blowfish文件](https://blowfish.page/docs/)

[Hugo文件](https://gohugo.io/documentation/)