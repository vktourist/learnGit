learnGit这个仓库是用来学习Git的。  
下面的内容主要是学习廖雪峰老师git教程<https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000>的笔记，以后工作过程中学到其他技巧的话再更新。
# 创建版本仓库
git init:
    把目标文件夹变为git的版本仓库，执行命令后会在文件夹下生成一个.git的版本控制文件夹。
    
# 添加文件到仓库
两步：  
    1、使用git add <file>,可反复多次使用，添加多个文件  
    2、使用命令git commit -m <message>提交。
    
# 查看仓库状态
git status:可以看到文件现在的状态。

# 查看修改的内容
git diff:

# 查看提交历史
git log：可以查看提交历史，以便确定要回退到哪个版本。    
git log --pretty=oneline：在一行查看

# 查看命令历史
git reflog：查看命令历史，以便确定要回到未来的哪个版本。

# 版本回退
git reset --hard HEAD^或 git reset --hard [版本号]

# 工作区和暂存区
工作区：目标目录就是工作区。  
版本库：工作区中有一个隐藏目录.git，这个就是git的版本库。  
暂存区：git版本库中有很多东西，其中有称为stage的暂存区，还有分支master，以及指针叫HEAD。  
git add 是把修改的内容添加到暂存区，git commit是把暂存区中的内容提交到分支。  
git diff HEAD -- readme.txt 查看工作区和版本库里面最新版本的区别。

# 撤销修改
* 场景1：当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令git checkout -- file。  
git checkout -- file 命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令。

* 场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令git reset HEAD <file>，就回到了场景1，第二步按场景1操作。
git reset HEAD <file> 可以把暂存区的修改撤销掉，重新放回工作区。

* 场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

# 删除文件
git rm test.txt  
git commit -m "remove test.txt"  
删错了，恢复版本：git checkout -- test.txt,这个命令其实使用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。  
命令git rm用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。

# 远程仓库
因为本地仓库和远程仓库是通过SSH进行安全传输的，所以需要通过ssh-keygen -t rsa -C "youremail@example.com" 生成公私钥，并把公钥添加到远程仓库中，这样远程仓库才能验证你的身份，才允许你提交。
## 添加远程仓库
要关联一个远程仓库，使用命令git remote add origin git@server-name:path/repo-name.git;
关联后，使用命令git push -u origin master第一次推送master分支的所有内容；
此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改。

## 从远程仓库克隆
git clone git@server-name:path/repo-name.git  
git支持多种协议，包括git原生协议和https,git原生协议较快。

# 分支管理
## 创建与合并分支
查看分支：git branch  
创建分支：git branch <name>  
切换分支：git checkout <name>  
创建并切换分支：git checkout -b <name>  
合并某分支到当前分支：git merge <name>  
删除分支：git branch -d <name>

## 解决冲突
当git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
解决冲突就是把git合并失败的文件手动编辑为我们希望的内容，再提交。
用git log --graph命令可以看到分支合并图。

# 分支管理策略
在实际开发中，我们应该几个基本原则进行分支管理：  
首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；  
那在哪干活呢？干活的话都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；  
其他的每个人都在dev分支上干活，每个人都有自己的分支，时不时的往dev分支上合并就可以了。  
git merge --no-ff -m "merge with no-ff" dev
合并分支时，加上--no-ff参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而fast forward合并就看不出来曾经做过合并。

# Bug分支
修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
当手头工作没有完成时，先把工作现场git stash保存一下，然后去修复bug，修复后，再git stash pop，回到工作现场。
还可以用git stash apply恢复，但是回复后，stash内容并不删除，需要在使用git stash drop来删除。  
git stash list 查看保存的现场。
总的来说，就是，在分支下进行的工作，如果不commit的话，回到master，就会显示出你在分支下你添加的工作。这个时候，你在master下修改完bug提交后，正在分支进行的工作也会提交了。为了避免这个情况，你就在分支下，git stash将工作隐藏，这个时候，切换到master时候，修改了bug，提交。分支的内容不会被提交上去。

# Feature分支
软件开发中，总有无穷无尽的新的功能要不断添加进来。
添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了，所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。  
开发一个新feature,最好新建一个分支；如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name> 强行删除。

# 多人协作
哪些分支需要推送，哪些不需要呢？
master分支是主分支，因此要时刻与远程同步；  
dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；  
feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。  
创建远程origin的dev分支到本地：git checkout -b dev origin/dev/  

多人协作的工作模式通常是这样：
首先，可以试图用git push origin <branch-name>推送自己的修改；
如果推送失败，则因为远程分支比你的本地更新，需要先用git pull试图合并；
如果合并有冲突，则解决冲突，并在本地提交；
没有冲突或者解决掉冲突后，再用git push origin <branch-name>推送就能成功！
如果git pull提示no tracking information，则说明本地分支和远程分支的链接关系没有创建，用命令git branch --set-upstream-to <branch-name> origin/<branch-name>。
这就是多人协作的工作模式，一旦熟悉了，就非常简单。  


小结
查看远程库信息，使用git remote -v；
本地新建的分支如果不推送到远程，对其他人就是不可见的；
从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。

# rebase
rebase操作可以把本地未push的分叉提交历史整理成直线；  
rebase的目的是使得我们在查看历史提交的变化时更容易，因为分叉的提交需要三方对比。

# 创建标签
git tag <tagname> 用于新建一个标签，默认为HEAD，也可以指定一个commit id；  
git tag -a <tagname> -m "message" 可以指定标签信息；  
git tag可以查看所有标签；  
git show <tagname> 可以查看标签信息，注意，标签不是按时间顺序列出，而是按字母排序的。
注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。

# 操作标签
git push origin <tagname>:可以推送一个本地标签到远程仓库；  
git push origin --tags:可以推送全部未推送过的本地标签；  
git tag -的<tagname>:删除一个本地标签；  
git push origin :refs/tags/<tagname>：删除一个远程标签。

# 使用码云
git remote add origin git@server-name:repo-name/learngit.git:关联远程仓库  
git remote rm origin :删除远程仓库  
git remote -v：查看远程仓库信息  
git remote add <远端仓库名>   git@server-name:repo-name/learngit.git： 关联多个不同仓库  
注意：git给远程库起的默认名称是origin，如果有多个远程库，我们需要用不同的名称来标识不同的远程库。

# 自定义git
git config --global color.ui true：让Git显示颜色，会让命令输出看起来更醒目。
## 忽略特殊文件
.gitignore文件：忽略某些文件时，需要编写.gitignore,.gitignore文件本身要放到版本库中，并且可以对.gitignore做版本管理。  
git add -f <filename>:-f 强制添加到git  
git check-ignore -v <filename>: 检查那个规则忽略了该文件。

## 配置别名
git config --global alias.st status:给status设置别名st；
--global参数是全局参数，也就是设置的命令在这台电脑的所有git仓库下都可以使用。配置Git的时候，加上--global是针对当前用户起作用的，如果不加，那只针对当前的仓库起作用。
配置文件放哪了？每个仓库的Git配置文件都放在.git/config文件中，而当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中。

# 搭建git服务器
https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/00137583770360579bc4b458f044ce7afed3df579123eca000
