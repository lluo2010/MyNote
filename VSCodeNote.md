# VSCode note


## 基本


1. 从左边栏拖到文件到右边,可以多视图显示
1. 在资源管理器tt(左边栏)上可以直接改文件名,如果是文件夹,可以在其边上选择+按钮新建文件
1. 在资源管理的文件或者文件夹中右键"在终端中打开"可以启动命令行
1. 在资源管理的文件abccc或者文件夹中右键"在查找器中展现"可以打开它所在的文件夹
1. 在资源管理的文件或者文件夹中右键"选择以进行比较"可以选择文件进行比较
1. ctrl+p可以快速找到并打开当前的文件
1. ctrl+shift+p调出命令板.命令面板提供了许多命令。输入 ? 到输入框得到可用的命令列表.你可以执行编辑命令，打开文件，搜索符号，查看一个文件的简要概要，使用同一个交互的窗口。
1. 预览markdown Ctrl+Shift+V

1. 


## 窗体切换
1. 在打开文件之间(tab页)切换:cmd+1,cmd+2
1. 在多屏之间切换:ctrl+1,ctrl+2
1. 切出一个新的屏:ctrl+\
1. 全屏:ctrl+cmd+f
1. 显示/隐藏侧边栏:ctrl+b
1. show explorer： ctrl+shift+e
1. show search： ctrl+shift+f
1. show debug： ctrl+shift+d
1. show git： cmd+shift+g




## 关于搜索

1. ctrl+shift+f:当前打开的和所在的文件夹全部搜索
1. ctrl+f: 当前打开文件搜索
1. ctrl+shift+j:搜索高级配置
在搜索框下面有两个输入框，你可以包含和排除文件。点击输入框的右边切换glob模式：

	- \* 匹配路径段里面0或多个除 / 以外的字符
	- ? 匹配路径段里面一个除 / 以外的字符
	- ** 匹配路径段的多个字符，包括 /
	- {} 用组的形式（列如： {**/*.html,**/*.txt} 匹配所有的html和文本文件）
	- [] 匹配指定的字符范围


## 光标
1. 指定多个光标(multi-cursor):按住Alt, 然后在需要的每一个地方用光标Click,就可以出现多个光标,然后就可以多这些地方执行一样的操作.
1. 回到之前/后的光标位置:Cmd+-, Cmd+Shift+-
1. 查找到执行的字符串后,移动到下/上一个目标: F3/Shift+F3
1. 当前选择的或者当前光标下的字符串,移动到下/上一目标:Ctrl+F3/Ctrl+Shift+F3

## 文件切换
1. 按住ctrl, 然后按tab键就可以浏览当前打开的所有文件. 松开ctrl键后就打开了选择的文件了.两个按键如果不松开一直在文件名上切换,松开会切换到文件.
1. 另一种快速打开tab页上文件的方法是直接按tab+文件的位置数目,比如第二个文件即tab+2就直接快速打开第二个文件.



## 关于命令板
1. ⌘p 只需简单的输入它的名字就让你导航到所有文件或者符号
1. ⌃⇧tab 显示你最后打开的一组文件路径
1. ⇧⌘p 调出命令面板
1. ⇧⌘o 在一个文件里，跳到一个指定符号的位置
1. ⌃g 在一个文件里，输入行数直接跳到指定行的位置


## 命令行打开vs code

### 操作
- 可以直接在命令行键入"code ."
- 也可以命令行键入"code c:\src\webapp"打开某一个项目

### 配置

上面这样操作前提是要做下面的配置:

1. 对于mac用户，我们需要通过设置使您能够从终端内启动vs code.首选运行vs code并打开命令面板（ ⇧⌘p ），然后输入 shell command 找到: install ‘code' command in path 。
1. windows和lunix用户安装过程自动把vs code的执行代码加到 path 环境变量中。


## 代码相关
1. 代码格式化: alt+shift+f
1. 智能感应: cmd+space
1. 预览（peek）: 选择一个符号，键入alt+f12，或者使用快捷菜单。
1. 转到定义: 选择一个符号，键入f12，或者使用快捷菜单。
1. 查找所有引用: 选择一个符号，键入shift+f12，或者使用快捷菜单。
1. 符号重命名: 选择一个符号，键入f2，或者使用快捷菜单。

1. 多行出现的单词的修改: 选中某个单词,然后ctrl+f2,然后就可以输入要修改的.注意, 如果在vim模式下,一定要先切换到可编辑再做上面操作,否则不成功.


## 关于markdown

vs code在编辑.md文档的时候，可以通过快捷键 ctrl+alt+v来预览最终的显示效果。可以通过vs code本身支持分屏显示,将.md和显示效果页一起显示,改变.md后效果页会马上更新.

但是目前對markdown格式顯示的支持不是很好.
可以通过用户设置里的markdown.styles来更改显示方式.

我们可以通过这样：

```
"markdown.styles": [ "file:///d:/user.css" ]
```
或者这样：

```
 "markdown.styles": [ "https://jasonm23.github.io/markdown-css-themes/screen.css" ]
```
来引用第三方的css文件。



## git
xxxxxxxx


## 其他
1. 在默认的情况下，vs code排除来自资源管理器的一些文件（比如：.git）。用files.exclude 来设置规则隐藏来自资源器的文件和文件夹。
1. 安装: 安装方法非常简单，用 F1 打开 命令行，然后输入 ext inst 来进行操作. 比如安装vscode-icons就F1或者ctrl+p后输入ext install vscode-icons.


## reference
1. [visual studio code入门(译)](http://www.jianshu.com/p/3dda4756eca5)
1. [visual studio code之版本控制](https://segmentfault.com/a/1190000004095224)
1. [version control](https://code.visualstudio.com/docs/editor/versioncontrol)
1. [VS Code开发技巧集锦](http://blog.csdn.net/tiantangyouzui/article/details/52163175)
1. [Visual Studio shortcut keys](http://www.dofactory.com/reference/visual-studio-shortcuts)