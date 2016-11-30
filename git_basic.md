# Git常用功能

## 几个概念

---

1. master  
    master是版本库创建后默认的分支.

    The name of the default branch that git creates for you when first creating a repo. In most cases, "master" means "the main branch". Most shops have everyone pushing to master, and master is considered the definitive view of the repo. But it's also common for release branches to be made off of master for releasing. Your local repo has its own master branch, that almost always follows the master of a remote repo.

1. HEAD
    指向版本库的当前提交.

    the current commit your repo is on. Most of the time HEAD points to the latest commit in your branch, but that doesn't have to be the case. HEAD really just means "what is my repo currently pointing at". 

1. origin  
    本地对应的远程仓库的默认名称.

    The default name that git gives to your main remote repo. Your box has its own repo, and you most likely push out to some remote repo that you and all your coworkers push to. That remote repo is almost always called origin, but it doesn't have to be.

1. origin/master  
    在本地的分支, 它其实是本地对服务端master分支的一个引用, 平常我们使用git fetch时, origin/master就是指向获取到的内容的.

    注意:master和origin/master其实不是两个不同分支, 它们其实是两个指针指向不同的提交.

    On your local though, another branch called as origin/master is created that references the remote server branch. 
The origin/master always tries to stay up to date with the remote repository branch. 

    When you fetch, git syncs the changes with your origin/master. It will not bring it on to your master branch, until you merge it. 

    Note that the master and origin/master are not essentially two different branches. They are just two pointers that point to different commits:
![origin/master](https://qph.ec.quoracdn.net/main-qimg-e047771fcb95cb4b28a460ff410b6185?convert_to_webp=true)



## git三个区域

---

可以将git简单的分为三个区域 :

1. 工作区（Working Directory）  
    平常我们都是在工作区工作的, 比如添加,删除修改文件,都马上体现在工作区.
1. 暂存区  
    将文件git add后,文件就处于暂存区.
1. 历史记录区history)  
    当代码git commit后就处于历史记录区了.

相关的图如下:

![git区域](https://pic004.cnblogs.com/news/201206/20120628_140459_1.jpg)

## 常用命令

---

命令|说明
-|:-
git status|查看git库状态
git add files|把当前文件放入暂存区域
git commit |提交代码,-m "xxx",包含了描述.
git checkout -- file|还原当前文件和暂存区一样
git checkout branchName|切换分支
git checkout -b branchName|新建分支
git merge branchName|合并分支
git pull origin branchName|拉取分支,包含合并
git fetch origin branchName|拉取分支,不包含合并
git push origin branchName|推送分支
git branch|查看本地所有分支信息, 如果带-a, 则查看本地和服务端的所有分支
git branch -d branchName|删除本地分支
git push origin --delete branchName|删除远程分支
git tag|列出所有tag
git tag -a xx -m "yy"|创建tag,-a后跟版本号, -m后描述
git push origin --delete tag tagName|删除远程tag
git push --tags| 把本地tag推送到远程


## 拉取代码

---

可以使用git fetch或者git pull拉去代码.
### 使用git fetch拉取代码 
如果远程没有代码更改, 就此结束.

如果有更新, 使用git merge origin/master合并代码, 分两种情况:

+ 代码没有冲突, 合并完成后就可以了.
+ 代码有冲突,会提示先commit或者stash代码再做合并.

1. 采用commit: commit后合并,如果有冲突,手工解决.
1. 采用stash: git stash,然后git merge origin/master,然后git stash apply stash@{0},有冲突的话解决下.

*** 至于选择commit还是stash, 要看目前代码是不是完成了一个完成的功能,如果是一个完整功能, 可以使用commit,如果不是,可以先stash,合并后再stash回来,继续实现功能. ***

拉代码时冲突:

关于冲突, 不管是采用commit,stash后再pull,或者先git fetch,然后stash/commit,然后merge,都需要解决冲突.

### 使用git pull拉取代码 
1. 如果本地没做修改, 直接git pull origin branchName拉取代码.
1. 如果本地有修改, 但是和上面的代码没有冲突的, git pull origin branchName后也就完成了.
1. 如果本地有修改, 且和上面的代码有冲突,需要使用下面两种处理:

    + 使用git commit,然后再 git pull
    + 使用git stash,然后git pull,然后git stash apply回来.

总结:

也就是说,只要上面的代码和下面的有冲突,就需要先使用git commit/stash.上面有变化但没有冲突则不需要.



## 推送代码

---

推送之前需要获取远程代码, 可以使用git fetch或者git pull进行.

### 使用git fetch
1. 提交代码到本地库  
    git commit -m "xxx"
2. 获取远程代码: git fetch origin branchName, 如果远程没有修改, 直接走4 git push.
3. 如果远程有修改, 执行git merge origin/branchName做合并,分下面两种,不管那种,解决后再git add,commit提交代码,然后走4 git push.

    + 没有冲突:上面的git merge直接成功.
    + 有冲突: 手工解决冲突
4. 执行git push origin branchName

### 使用git pull
1. 提交代码到本地库  
    git commit -m "xxx"
2. 获取远程代码: git pull origin branchName, 如果远程没有修改, 直接走4 git push.
3. 如果远程有修改, 分下面两种,不管那种,解决后再git add,commit提交代码,然后走4 git push:

    + 没有冲突: 直接自动合并了, 需要用户确认下.
    + 有冲突: 手工解决冲突

4. 执行git push origin branchName.
 
总结:

1. 如果远程有代码更改,即使是和本地修改的不是同一个文件(即不会有冲突),push代码前也需要先pull下远程的代码.
1. 拉下的代码需要合并fetch需要显式合并,pull已经包含了合并, 如果没有冲突的直接就合并成功了.
1. 有冲突时, 合并后需要手工解决冲突.


### git fetch和git pull区别:
1. git fetch相当于是从远程获取最新版本到本地，不会自动merge.
1. git pull相当于是从远程获取最新版本并merge到本地.可以理解为git fetch和git merge的合并.
1. git fetch只是获取远程的分支的最新版本到本地，但不merge,git pull获取后还会merge. 在实际使用中，git fetch更安全一些,因为在merge前，我们可以查看更新情况，然后再决定是否合并.



## 回退

---

### 指定文件回退
1. 工作区回退到最近的(HEAD)  
    git checkout -- filename
1. 暂存区(stage)回到最近(HEAD)  
    git reset -- filename
1. 工作区,暂存区(stage)一起回退到最近的(HEAD)  
    git checkout HEAD -- filename
1. 工作区,暂存区(stage)一起回退到指定提交  
    git checkout commitid -- filename       //reset --hard不可以, 因为--hard不能后面跟path
1. 工作区回退到指定提交
    需要两步: git checkout commitID -- filename     git reset -- filename

### 所有的回退
1. 工作区和暂存区(Stage)都回到最近(HEAD)  
    git reset --hard        //等价于git reset HEAD --hard
1. 工作区和暂存区(Stage)都回到指定提交
    git reset commitid --hard 
    
从上面可以看出, 如果是针对文件的,优先考虑checkout, 针对整个的,优先考虑reset. checkout主要针对是工作区, reset主要针对的是暂存区和索引区(历史).


## 版本定位

---

1. 到当前本地历史最新: git reset --hard head, head也可省略.
1. 到历史最新: git pull origin branchName




## 比较区别变化

---

如果查看某个版本和之前版本的变化,使用git show.

如果需要查看两次提交之间的变动.特别是不相邻的两次之间的变动,使用git diff:

功能|命令|其他描述
-|:-|:-
某个文件与前一个版本区别|git show commitid filename|比较该文件指定版本与之前版本的区别.如果省略commitid,表示的是当前版本的文件与上一个版本文件的区别:git show filename
某个文件指定两个版本的区别|git diff commitid1 commit id2 filename|commitid1和commitid2的顺序影响显示, 建议早的在前,晚的在后.
某个提交与之前提交的变化|git show commitid|指定某个提交和之前提交的区别
比较两个版本间的差别|git diff commitid1 commitid2

## 查看详情

---

1. 某个提交的详情.

1. 查看某个tag信息: git show tagName

todo:xxx...

## 查看历史

---

功能|命令
-|:-
查看全部历史| git log
查看某个分支历史| git log branchName
查看某个文件历史| git log filename


## 关于diff

---

![diff图](https://pic004.cnblogs.com/news/201206/20120628_140502_4.jpg)


## 关于git stash

---

经常有这样的事情发生，当你正在进行项目中某一部分的工作，里面的东西处于一个比较杂乱的状态，而你想转到其他分支上进行一些工作。问题是，你不想提交进行了一半的工作，否则以后你无法回到这个工作点。解决这个问题的办法就是git stash命令。

git stash可以备份当前的工作区的内容，从最近的一次提交中读取相关内容，让工作区保证和上次提交的内容一致。同时，将当前的工作区内容保存到Git栈中.

注意: stash的list是对所有分支共享的.

相关的命令如下:

命令|描述
-|:-
git stash|对当前的暂存区和工作区状态进行保存,git stash save "xx"可以添加描述
git stash list|列出所有的stash
git stash pop stash@{x} | 从Git栈中读取最近一次保存的内容，恢复工作区的相关内容。
git stash apply stash@{x}|不删除已恢复的进度，其他同git stash pop 
git stash drop stash@{x}|删除某一个进度，默认删除最新进度 
git stash clear |删除所有进度 


## tag操作

---
命令|描述
-|:-
git tag|列出所有tag
git show tagName|查看某个tag信息
git tag -a xx -m "yy"|创建tag,-a后跟版本号, -m后描述
git push --tags| 把本地tag推送到远程
git push origin --delete tag tagName|删除远程tag


## sourceTree上动作对应的命令

---

1. Unstage到Stage: git add .
1. 取消Stage files移动到Unstage files:
    + 单个文件: git reset -- filename
    + 所有文件: git reset
1. ...

## 其他

---

1. 修改最后一次提交  
    git commit --amend -m ”YOUR-NEW-COMMIT-MESSAGE”

    注意:
    + 修改后,原来的提交信息及记录不会存在分支历史中.
    + 可以修改commit的message描述.
    + 如果Stage(暂存区)有文件的变动,也会被提交
1. ~和^的区别
    + HEAD^和HEAD~都是HEAD, HEAD~等价于HEAD~1,HEAD^等价于HEAD^1, 
    + “^”代表父提交,当一个提交有多个父提交时，可以通过在”^”后面跟上一个数字，表示第几个父提交，”^”相当于”^1”.
    + ^和~的区别是^后面不管跟什么数字,HEAD^x永远指向当前的上一层, 而HEAD~x则根据数字x不同而指向不同的层.





## Reference

---
1. [七个你无法忽视的Git使用技巧](https://www.oschina.net/news/68437/seven-git-hacks-you-just-cannot-ignore)
1. [图解Git](http://news.cnblogs.com/n/147941/)




AlarmClock.EXTRA_MESSAGE

