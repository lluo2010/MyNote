# Homebrew

## 什么是Homebrew
linux系统很多软件包需要依赖其他包，所以安装时需要也安装依赖的包. 好在当前主流的两大发行版本都自带了解决方案，Red hat有yum，Ubuntu有apt-get.
mac os没有类似的东西，但是用Homebrew可以提点.Homebrew简称brew，是Mac OSX上的软件包管理工具，能在Mac中方便的安装软件或者卸载软件，可以说Homebrew就是mac下的apt-get、yum神器

## 安装
Homebrew的安装非常简单，实现下面就可以了:

        ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
上面可能需要翻墙.

## 使用

命令|功能
-|:-
brew [info | home | options ] [FORMULA...]
brew install FORMULA...   | 安装包
brew uninstall FORMULA... | 卸载包
brew search [foo]         | 搜索包
brew list [FORMULA...]    | 显示已经安装的软件列表
brew update               |更新brew
brew upgrade [FORMULA...] | 更新包，不跟包名就是更新所有包
brew pin/unpin [FORMULA...]|
man brew    |显示brew的帮助
brew home   |打开brew主页
