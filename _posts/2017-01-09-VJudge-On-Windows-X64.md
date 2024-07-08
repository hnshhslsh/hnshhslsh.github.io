---
layout: post
title: "VJudge Windows X64快速部署说明"
categories:
  - Web
tags:
  - Vjudge
  - Windows
last_modified_at: 2019-05-25 15:44:56 +08:00





---

* content
{:toc}
无须自行搭建环境，解压简单配置便可在64位Windows上运行带有VJudge（virtual-judge）的Web服务器。10分钟完成部署，在没有测试数据的情况下进行算法竞赛的比赛与训练。本版本的Vjudge为在局域网中比赛环境的进行了优化，详见[https://github.com/hnshhslsh/virtual-judge](https://github.com/hnshhslsh/virtual-judge) 。

本文在本人新博客的链接：[http://www.myblog.link/2017/01/09/VJudge-On-Windows-X64/](http://www.myblog.link/2017/01/09/VJudge-On-Windows-X64/)



## 免责申明
1. 本部署方法旨在快速地部署vjudge,并没有考虑稳定性、兼容性与安全性等（存在各种默认弱口令……），对这些有需求的朋友请自行研究或使用官方推荐方法，本人不对该方法造成的任何损失负责。
2. 该压缩包中的JspStudy程序来自[http://www.phpstudy.net/a.php/197.html](http://www.phpstudy.net/a.php/197.html) ，除了放入vjudge网站数据，本人未对其进行任何修改，且对其存在的任何问题（包括但不限于版权问题，安全性问题等）不负任何责任。
3. 该压缩包中的Vjudge网站为本人的修改版本，源码地址为[ https://github.com/hnshhslsh/virtual-judge/tree/4da48a5c4e0c6cdde2659ff28050061721a8c415]( https://github.com/hnshhslsh/virtual-judge/tree/4da48a5c4e0c6cdde2659ff28050061721a8c415)   修复/添加了对一些OJ的支持，也修改了原版部分逻辑，不对修改负任何责任，介意者请自行编译原版。


## 使用方法

1. 下载并解压压缩包，其中包含了运行环境和Vjudge网站(2019-05更新)：
[https://download.csdn.net/download/hnshhslsh/11203539](https://download.csdn.net/download/hnshhslsh/11203539)
2. 安装解压后文件夹中的`Redis-x64-3.2.100.msi` ， 如安装失败则先自行安装 [Microsoft .NET Framework 4.5](https://www.microsoft.com/zh-CN/download/details.aspx?id=42642) 再重试
3. 修改 `\JspStudy2016\WWW\WEB-INF\classes\config.properties` 文件中的 `remoteAccountsConfig.location=`与`httpClientConfig.location=` 属性，为最外层目录下的`remote_accounts.json`与`http_client.json`文件的绝对路径（也可自行移动这两个文件，但需要保证绝对路径正确）
4. 修改 `remote_accounts.json` ，添加自行注册的各OJ账户，注意在`SGU`中需填写 Codeforces 账号
5. 运行`JspStudy2016\JspStudy.exe` ，启动其集成的mysql与tomcat
6. 进入[http://localhost:8080/](http://localhost:8080/) ，开始使用吧！(如果上一步的Apache也启动了就可以不用加:8080了。)其它机器访问`服务器IP地址:8080`即可进入Vjudge，记得设置Windows防火墙。

## 历史版本
- 201905
  - [https://download.csdn.net/download/hnshhslsh/11203539](https://download.csdn.net/download/hnshhslsh/11203539)
- 201707
  - 第一部分 [http://download.csdn.net/download/hnshhslsh/9917029](http://download.csdn.net/download/hnshhslsh/9917029)
   - 第二部分 [http://download.csdn.net/download/hnshhslsh/9917032](http://download.csdn.net/download/hnshhslsh/9917032)
- 201701
   - 第一部分 [https://download.csdn.net/download/hnshhslsh/9733244](https://download.csdn.net/download/hnshhslsh/9733244)
   - 第二部分 [https://download.csdn.net/download/hnshhslsh/9733245](https://download.csdn.net/download/hnshhslsh/9733245)