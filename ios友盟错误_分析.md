# 友盟统计错误分析

## 概述

---

错误分析是友盟为移动开发者提供的Crash收集和分析工具，帮助开发者监测App在移动设备上的运行状况，及时发现并解决错误，提升App的稳定性。

它提供的功能如下:

1. 通过友盟后台网站管理错误内容。

    你可以按照版本、UUID、操作系统、机型筛选错误; 还可以根据不同的条件为错误添加标签，便于快速分类及查找错误。
1. 通过友盟错误分析工具定位错误。

    你可以在友盟后台网站批量导出错误，并借助命令行工具将错误快速定位到具体的代码行数。

    通过分析工具定位错误：登陆友盟统计,在错误分析中可以看到错误趋势和错误列表，如下图：

    ![错误趋势图](http://120.27.145.142:8090/download/attachments/4556986/585328A2-260E-4C3C-ABF2-4BB7AB2A38FD.png?version=1&modificationDate=1486373522895&api=v2)

    在错误列表中点击错误进入到错误详情页，如下图：

    ![错误详情图](http://120.27.145.142:8090/download/attachments/4556986/C6D189D2-899E-4AB2-B7F5-F35497F09558.png?version=1&modificationDate=1486373869431&api=v2)

    然后点击红色框按钮，在报表中心查看要下载的.csv错误文件，并下载错误文件。



## 错误分析工具的使用

---

前提：确保dSYM文件存在。(在我们每次上传iTunes connect之后会有一个 .xcarchive的文件，要确保对应的 xxx.dSYM 文件在 ~/Library/Developer/Xcode/ 或该路径的子目录下。对于每一个产品发布时archive操作会将dsym文件存放到~/Library/Developer/Xcode/Archives路径下,以便后续用工具分析错误。）

步骤:
 
1. 下载错误分析工具[umcrashtool](http://dev.umeng.com/files/download/umcrashtool.zip)，并解压zip得到umcrashtool文件，将umcrashtool与刚才已下载的xxx.csv文件放入同一目录下。
1. 在terminal中相继拖入umcrashtool 和csv文件，然后回车。
1. 定位后的代码及行数会写入错误分析-symbol.csv文件，与原文件在同一目录下。用工具打开新生成的xxx-symbol.csv文件，便可查看错误发生的源码文件及行数。



## 解析出现的问题

---

1. csv文件打开有乱码？  

    -->csv文件使用的是UTF8编码格式，需要选用相应的格式打开，在Mac平台可以用系统自带的Numbers或免费软件LibreOffice打开。目前的Microsoft Office for Mac 打开会有乱码的问题。

1. 使用umcrashtool没有正确的翻译出错误？  

    -->首先请确保dSYM文件存放在 ~/Library/Developer/Xcode/或者它的子目录下。另外, 目前的错误捕捉工具针对一些系统信号导致的崩溃信息，存在无法解析的情况，最后可能是dsym文件提供的信息量不够，导致部分解析失败。