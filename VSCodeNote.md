# VS code note

## 基本

1. CMD+1,CMD+2在打开的文件的tab上切换
1. 从左边栏拖到文件到右边,可以多视图显示abc
1. 在资源管理器tt(左边栏)上可以直接改文件名,如果是文件夹,可以在其边上选择+按钮新建文件
1. 在资源管理的文件或者文件夹中右键"在终端中打开"可以启动命令行
1. 在资源管理的文件abccc或者文件夹中右键"在查找器中展现"可以打开它所在的文件夹
1. 在资源管理的文件或者文件夹中右键"选择以进行比较"可以选择文件进行比较
1. CTRL+P可以快速找到并打开当前的文件
1. CTRL+Shift+p调出命令板.命令面板提供了许多命令。输入 ? 到输入框得到可用的命令列表.你可以执行编辑命令，打开文件，搜索符号，查看一个文件的简要概要，使用同一个交互的窗口。
1. ctrl+cmd+f:全屏




## 关于搜索

1. ctrl+shift+F:当前打开的和所在的文件夹全部搜索
1. ctrl+f: 当前打开文件搜索
1. ctrl+shift+j:搜索高级配置
在搜索框下面有两个输入框，你可以包含和排除文件。点击输入框的右边切换glob模式：

	- \* 匹配路径段里面0或多个除 / 以外的字符
	- ? 匹配路径段里面一个除 / 以外的字符
	- ** 匹配路径段的多个字符，包括 /
	- {} 用组的形式（列如： {**/*.html,**/*.txt} 匹配所有的HTML和文本文件）
	- [] 匹配指定的字符范围

## 文件切换
1. 按住ctrl, 然后按tab键就可以浏览当前打开的所有文件. 松开ctrl键后就打开了选择的文件了.两个按键如果不松开一直在文件名上切换,松开会切换到文件.
1. 另一种快速打开tab页上文件的方法是直接按tab+文件的位置数目,比如第二个文件即tab+2就直接快速打开第二个文件.



## 关于命令板
1. ⌘P 只需简单的输入它的名字就让你导航到所有文件或者符号
1. ⌃⇧Tab 显示你最后打开的一组文件路径
1. ⇧⌘P 调出命令面板
1. ⇧⌘O 在一个文件里，跳到一个指定符号的位置
1. ⌃G 在一个文件里，输入行数直接跳到指定行的位置


## 命令行打开vs code

### 操作
- 可以直接在命令行键入"code ."
- 也可以命令行键入"code C:\src\WebApp"打开某一个项目

### 配置

上面这样操作前提是要做下面的配置:

1. 对于Mac用户，我们需要通过设置使您能够从终端内启动VS Code.首选运行VS code并打开命令面板（ ⇧⌘P ），然后输入 shell command 找到: Install ‘code' command in PATH 。
1. Windows和Lunix用户安装过程自动把VS Code的执行代码加到 PATH 环境变量中。




## 关于markdown

VS Code在编辑.md文档的时候，可以通过快捷键 Ctrl+Alt+v来预览最终的显示效果。可以通过vs code本身支持分屏显示,将.md和显示效果页一起显示,改变.md后效果页会马上更新.

但是目前對markdown格式顯示的支持不是很好.
可以通过用户设置里的markdown.styles来更改显示方式.

我们可以通过这样：

```
"markdown.styles": [ "file:///D:/user.css" ]
```
或者这样：

```
 "markdown.styles": [ "https://jasonm23.github.io/markdown-css-themes/screen.css" ]
```
来引用第三方的CSS文件。



## git
xxxxxxxx


## 其他
1. 在默认的情况下，VS Code排除来自资源管理器的一些文件（比如：.git）。用files.exclude 来设置规则隐藏来自资源器的文件和文件夹。

## Reference
1. [Visual Studio Code入门(译)](http://www.jianshu.com/p/3dda4756eca5)
1. [Visual Studio Code之版本控制](https://segmentfault.com/a/1190000004095224)
1. [Version Control](https://code.visualstudio.com/Docs/editor/versioncontrol)
