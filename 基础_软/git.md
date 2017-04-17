2016-09-22 周三 晴
目录
[toc]

Git是目前世界上最先进的分布式版本控制系统
(版本控制系统，其实只能跟踪文本文件的改动)

#1. 安装git
##1.1 Linux
```Bash
$ sudo apt-get install git
$ git --version 
git version 2.1.4

#其它Linux版本，可以直接通过源码安装。下载、解压后
$ ./config
$ make
$ sudo make install
```
##1.2 windows
Windows下要使⽤用很多Linux/Unix的工具时，需要Cygwin这样的模拟环境，Git也一样。
有人已经把模拟环境和Git都打包好了，名叫msysgit，只需要下载一个单独的exe安装程序，其他什么也不用装
msysgit是Windows版的Git，从 http://msysgit.github.io/ 下载，然后按默认选项安装即可。
安装完成后，使用 开始菜单 -> “Git” -> “Git Bash”来运行

###1.2.1 命令提示符PS1
安装了cygwin，发现命令提示符和用户名机器名不在同一行显示, 而且花花绿绿的太晃眼.
可以设置环境变量PS1，写入.bashrc中
```Bash
export PS1='[\u@\h \w]$ '                # [Roger@Roger-T450s /e/MyDocs/git_test]$
export PS1='\[\033[32m\]\$ \[\033[0m\]'  # 只显示一个绿色的$
```
###1.2.2 Cygwin乱码
Option => Text:
Locale=C，Character Set=GBK

##1.3 设置用户名和邮箱
安装完成后，还需要最后一步设置，在命令行输入：
```Bash
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```
#2. 使用git
##2.1 init---创建版本库(repository)
版本库可以简单理解成一个目录，这个目录里面的所有文件都可以被Git管理起来，每个文件的修改、删除，Git都能跟踪
```Bash
$ mkdir git_test
$ cd git_test/
$ git init        # 把这个目录变成Git可以管理的仓库
Initialized empty Git repository in E:/MyDocs/git_test/.git/ 
```
##2.2 add & commit---把文件添加到版本库
```Bash
$ git add readme.txt
warning: LF will be replaced by CRLF in readme.txt.
The file will have its original line endings in your working directory.
$ git commit -m "wrote a readme file"  #把文件提交到仓库
[master (root-commit) efc5467] wrote a readme file
 1 file changed, 2 insertions(+)   #1文件被改动, 插入2行
 create mode 100644 readme.txt 
```

##2.3 status & diff---查看更改内容
修改一下readme.txt
```Bash
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   readme.txt

no changes added to commit (use "git add" and/or "git commit -a")

$ git diff readme.txt
diff --git a/readme.txt b/readme.txt
index 46d49bf..9247db6 100644
--- a/readme.txt
+++ b/readme.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.

$ git add readme.txt
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
        modified:   readme.txt

$ git commit -m "add a word --- distributed"
[master ceb257f] add a word --- distributed
 1 file changed, 1 insertion(+), 1 deletion(-)
$ git status
On branch master
nothing to commit, working tree clean
```
##2.4 log---查看改动历史记录
修改readme.txt, 然后git add, git commit
可以用git log查看改动历史记录 (加上--pretty=oneline参数)
```Bash
$ git log
commit df04479b2a086baf754dc4dcbec02a3d5aa0a280
Author: Roger Zhu <yaksazhu@163.com>
Date:   Wed Sep 21 10:36:15 2016 +0800
    append GPL
commit ceb257f517c67d149a608cb1c97a565eb5df9b55
Author: Roger Zhu <yaksazhu@163.com>
Date:   Wed Sep 21 09:54:56 2016 +0800
    add a word --- distributed
commit efc5467d007b8116447b4f527065118a1750d9bb
Author: Roger Zhu <yaksazhu@163.com>
Date:   Wed Sep 21 09:40:51 2016 +0800
    wrote a readme file
$ git log --pretty=oneline
df04479b2a086baf754dc4dcbec02a3d5aa0a280 append GPL
ceb257f517c67d149a608cb1c97a565eb5df9b55 add a word --- distributed
efc5467d007b8116447b4f527065118a1750d9bb wrote a readme file  
```
git的commit ID不是1、2、3……递增的数字, 而是一个SHA1计算出来的非常大的十六进制数.

##2.5 reset---版本回退
在Git中，用HEAD表示当前版本(最新的),
上一个版本就是HEAD^，上上一个版本就是HEAD^^, 上100个版本写成HEAD~100
```Bash
$ git log --pretty=oneline
df04479b2a086baf754dc4dcbec02a3d5aa0a280 append GPL
ceb257f517c67d149a608cb1c97a565eb5df9b55 add a word --- distributed
efc5467d007b8116447b4f527065118a1750d9bb wrote a readme file
$ git reset --hard HEAD^    #回退到"上一版本"
HEAD is now at ceb257f add a word --- distributed
$ git log --pretty=oneline  #可以看到原来log最上面的一条没有了
ceb257f517c67d149a608cb1c97a565eb5df9b55 add a word --- distributed
efc5467d007b8116447b4f527065118a1750d9bb wrote a readme file

# 版本回退了.
# 如果想撤销回退怎么办? 只要命令行窗口还没关, 能查到原来那个commit的ID就行
# 版本号没必要写全，前几位就可以了，Git会自动去找。
$ git reset --hard df04479b2a086baf754dc4dcbec02a3d5aa0a280
HEAD is now at df04479 append GPL
$ git log --pretty=oneline
df04479b2a086baf754dc4dcbec02a3d5aa0a280 append GPL
ceb257f517c67d149a608cb1c97a565eb5df9b55 add a word --- distributed
efc5467d007b8116447b4f527065118a1750d9bb wrote a readme file
```
Git的版本回退速度非常快，因为Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向“append GPL”改为指向“add distributed”

如果命令行窗口已经关闭了, 找不到commit ID了怎么办?
```Bash
$ git reflog
df04479 HEAD@{0}: reset: moving to df04479b2a086baf754dc4dcbec02a3d5aa0a280
ceb257f HEAD@{1}: reset: moving to HEAD^
df04479 HEAD@{2}: commit: append GPL
ceb257f HEAD@{3}: commit: add a word --- distributed
efc5467 HEAD@{4}: commit (initial): wrote a readme file
```
##2.6 工作区&暂存区
工作区（Working Directory）：就是你在电脑里能看到的目录
版本库（Repository）：工作区有一个隐藏目录“.git”，这个不算工作区，而是Git的版本库。

Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。
第一步“git add”，实际上就是把文件修改添加到暂存区（Stage）；
第二步“git commit”，实际上就是把暂存区的所有内容提交到当前分支。暂存区就没有任何内容了


##2.7 管理修改
为什么Git比其他版本控制系统设计得优秀，因为<font color=red>Git跟踪并管理的是修改，而非文件</font>。
每次修改，如果不add到暂存区，那就不会加入到commit中

##2.8 checkout---撤销(工作区的)修改
```Bash
$ git checkout -- readme.txt
```
命令git checkout -- readme.txt意思就是，把readme.txt文件在工作区的修改全部撤销，这里有两种情况：
1. 修改后还没有被add --- 撤销修改就回到和版本库一模一样的状态；
2. 修改后已经被add，又作了修改 --- 撤销修改就回到添加到暂存区后的状态。
总之，就是让这个文件<font color=red>回到最近一次 git commit或git add时的状态</font>。

【注意：】`git checkout -- file`命令中的“--”很重要，没有它就变成了“创建一个新分支”的命令.

> 如果修改已经add了, 想恢复到上次commit时的样子, 
```Bash
git reset HEAD file
git checkout -- readme.txt
```
小结:
场景1：当你改乱了工作区某个文件的内容，想直接丢弃【工作区】的修改时，⽤用命令`git checkout -- file`。
场景2：当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，①`git reset HEAD file` ②按场景1操作。
场景3：已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。

##2.9 rm---删除文件
```Bash
$ git rm test.txt
rm 'test.txt'
$ git commit -m "remove test.txt"
```
文件就从版本库中被删除了(本地的test.txt也被删除)

#3. 远程仓库GitHub
可以自己搭建一台运行Git的服务器，不过现阶段，为了学Git先搭个服务器绝对是小题大作。
GitHub就是提供Git仓库托管服务的，所以，只要注册一个GitHub账号，就可以免费获得Git远程仓库。

##3.1 创建远程仓库

由于你的本地Git仓库和GitHub仓库之间的传输是通过SSH加密的，所以，需要一点设置：
第1步：创建SSH Key。
```Bash
$ ssh-keygen -t rsa -C "youremail@example.com"  #⼀一路回车，使用默认值即可. 也无需设置密码
```
可以在用户主目录⾥里找到.ssh目录，⾥里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，可以放心地告诉任何人
第2步：登陆GitHub，打开“Account settings”，“SSH Keys”页面, 点“Add SSH Key”，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容

GitHub允许你添加多个Key。假定你有多个电脑，你可以在公司提交，也可以在家里提交，只要把每台电脑的Key都添加到GitHub，就可以在每台电脑上往GitHub推送了。

最后友情提示，在GitHub上免费托管的Git仓库，任何人都可以看到（但只有你自己才能改）。所以，不要把敏感信息放进去。
如果你不想让别人看到Git库，有两个办法，⼀一个是交费，让GitHub把公开的仓库变成私有的，这样别人就看不见了（不可读更不可写）。另一个办法是自己动手，搭一个Git服务器

##3.2 添加远程库 & 推送
你已经在本地创建了一个Git仓库后，又想在GitHub创建一个Git仓库，并且让这两个仓库进行远程同步，这样，GitHub上的仓库既可以作为备份，又可以让其他人
通过该仓库来协作
1. 登陆GitHub，在右上角找到“Create a new repo”按钮，创建一个新的仓库, 如: git_test_2016
2. 把一个已有的本地仓库与之关联，然后，把本地仓库的内容推送到GitHub仓库
```Bash
$ git remote add origin https://github.com/yaksazhu/git_test_2016.git
$ git push -u origin master
Username for 'https://github.com': yaksazhu@163.com
Counting objects: 21, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (21/21), 1.66 KiB | 0 bytes/s, done.
Total 21 (delta 6), reused 0 (delta 0)
remote: Resolving deltas: 100% (6/6), done.
To https://github.com/yaksazhu/git_test_2016.git
 * [new branch]      master -> master
Branch master set up to track remote branch master from origin.
```
添加后，远程库的名字就是origin，这是Git默认的叫法，也可以改成别的，但是origin这个名字一看就知道是远程库。
git push命令，实际上是把当前分支master推送到远程。
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

从现在起，只要本地作了提交，就可以通过命令把本地master分支的最新修改推送至GitHub
```
$ git push origin master  #把本地master分支的最新修改推送至GitHub
```
注: Git支持多种协议，默认的git://使用ssh，也可以使用https等其他协议
使用https除了速度慢以外，还有个最大的麻烦是每次推送都必须输入口令，但是在某些只开放http端口的公司内部就无法使用ssh协议而只能用https。
`$ git remote add origin https://github.com/yaksazhu/git_test_2016.git`
`$ git remote add origin git@github.com:yaksazhu/git_test_2016.git`

##3.3 从远程库克隆
上次我们讲了先有本地库，后有远程库的时候，如何关联远程库。
现在，假设我们从零开发，那么最好的方式是先创建远程库，然后，从远程库克隆。
1. 登陆GitHub，创建一个新的仓库，名字叫gitskills (勾选Initialize this repository with a README，这样GitHub会自动为我们创建一个
README.md文件)
2. 用命令`git clone`克隆一个本地库
```Bash
$ git clone git@github.com:yaksazhu@163.com/gitskills.git
```

#4. 分支管理
Git的分支是与众不同的，无论创建、切换和删除分支，Git在1秒钟之内就能完成！⽆无论你的版本库是1个文件还是1万个文件。

##4.1 创建与合并分支
HEAD严格来说不是指向提交，而是指向master，master才是指向提交的，所以，HEAD指向的就是当前分支。
```
查看分支:           git branch
创建分支:           git branch name
切换分支:           git checkout name
创建+切换分支:      git checkout -b name
合并分支到当前分支:  git merge name
删除分支:           git branch -d name
```

①每次提交，master分支都会向前移动一步，这样，随着你不断提交，master分支的线也越来越长。

②创建新的分支---dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上
(Git创建一个分支很快，因为除了增加一个dev指针，改改HEAD的指向，工作区的文件都没有任何变化！)


③从现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变


④假如我们在dev上的工作完成了，就可以把dev合并到master上。
Git怎么合并呢？最简单的方法，就是直接把master指向dev的当前提交，就完成了合并
(Git合并分支也很快！就改改指针，工作区内容也不变！)


⑤合并完分支后，甚至可以删除dev分支。
删除dev分支就是把dev指针给删掉，删掉后，我们就剩下了一条master分支：

```Bash
# ②创建&切换到dev分支.
$ git checkout -b dev   # -b参数表示创建并切换, 相当于 git branch dev; git checkout dev两条命令
Switched to a new branch 'dev'
$ git branch    #令查看当前分支
* dev
  master

# ③readme.txt做某些修改, 提交
$ git add readme.txt
$ git commit -m "branch test: modify readme.txt"
[dev fa3c346] branch test: modify readme.txt
 1 file changed, 2 insertions(+)

# dev分支的工作完成，切换回master分支, 观察readme.txt文件
$ git checkout master
Switched to branch 'master'
Your branch is up-to-date with 'origin/master'.
# 再查看一下readme.txt文件，刚才添加的内容不⻅见了

# ④把dev分支的工作成果合并到master分支上
$ git merge dev
Updating b74a687..fa3c346
Fast-forward
 readme.txt | 2 ++
 1 file changed, 2 insertions(+)
# 注意上面的Fast-forward信息，Git告诉我们，这次合并是“快进模式”，也就是直接把master指向dev的当前提交，所以合并速度非常快。
# 合并后，再查看readme.txt的内容，就可以看到，和dev分支的最新提交是完全一样的。

# ⑤合并完成后，就可以放心地删除dev分支了
$ git branch -d dev
Deleted branch dev (was fa3c346).
$ git branch
* master # 只剩master分支了
```

因为创建、合并和删除分支非常快，所以Git鼓励你使用分支完成某个任务，合并后再删掉分支，这和直接在master分支上工作效果是一样的，但过程更安全。

##4.2 解决冲突
```Bash
$ git checkout -b conflict
# 修改LICENSE.txt
$ git add LICENSE.txt
$ git commit -m "conflict test--LICENSE.txt"

$ git checkout master
# 修改LICENSE.txt
$ git add LICENSE.txt
$ git commit -m "conflict test_master--LICENSE.txt"
```
如上, 2个分支各有新的提交, 变成这样:

这种情况下，Git无法执行“快速合并”，只能试图把各自的修改合并起来，但这种合并就可能会有冲突:
```Bash
$ git merge feature1
Auto-merging LICENSE.txt
CONFLICT (content): Merge conflict in LICENSE.txt
Automatic merge failed; fix conflicts and then commit the result.
# Git告诉我们，LICENSE.txt文件存在冲突，必须手动解决冲突后再提交。

# git status也可以告诉我们冲突的文件
$ git status
On branch master
Your branch is ahead of 'origin/master' by 2 commits.

Unmerged paths:
   (use "git add/rm <file>..." as appropriate to mark resolution)

   both modified:      LICENSE.txt
no changes added to commit (use "git add" and/or "git commit -a")

# 再查看LICENSE.txt, 其内容变成了这样:
$ cat LICENSE.txt
<<<<<<< HEAD
xxxxxxxxxxxxxxx
=======
222222222222222
>>>>>>> conflict

# 修改冲突的LICENSE.txt, 再次提交
$ git add LICENSE.txt
$ git commit -m "解决冲突问题"
```
现在，master分支和feature1分支变成了这样：

用带参数的git log也可以看到分支的合并情况：
```Bash
$ git log --graph --pretty=oneline --abbrev-commit
*   d86daa2 解决冲突问题
|\
| * 9d4479a conflict test--LICENSE.txt
* | 746e4b3 conflict test_master--LICENSE.txt
|/
* fa3c346 branch test: modify readme.txt
* b74a687 remove test.txt
* 3f1bffa add test.txt
* 1daac8c remove test.txt
* 5ee13fb add test.txt
* 8adc53b understand how stage works
* df04479 append GPL
* ceb257f add a word --- distributed
* efc5467 wrote a readme file
```
然后，可以删除feature1分支了
`$ git branch -d feature1`

##4.3 分支管理策略
###4.3.1 普通模式合并
小结:合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，⽽而fast forward合并就看不出来曾经做过合并。

通常，合并分支时，如果可能，Git会用“Fast forward”模式，但这种模式下，删除分支后，会丢掉分支信息。
如果要强制禁用“Fast forward”模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
```Bash
# 创建并切换dev分支：
$ git checkout -b dev
Switched to a new branch 'dev'
# 修改readme.txt文件，并提交一个新的commit
$ git add readme.txt
$ git commit -m "add 诗词"

# 切换回master
$ git checkout master
# 合并dev分支，注意--no-ff参数，表示禁用“Fast forward”：
$ git merge --no-ff -m "merge with no-ff" dev  # -m参数, 因为本次合并要创建一个新的commit
Merge made by the 'recursive' strategy.
 readme.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
# 合并后，我们用git log看看分支历史 (不会丢掉分支信息)
$ git log --graph --pretty=oneline --abbrev-commit
*   4c312eb merge with no-ff
|\
| * 783ca48 add 诗词
|/
*   d86daa2 解决冲突问题
|\
| * 9d4479a conflict test--LICENSE.txt
* | 746e4b3 conflict test_master--LICENSE.txt
|/
* fa3c346 branch test: modify readme.txt
* b74a687 remove test.txt
* 3f1bffa add test.txt
* 1daac8c remove test.txt
* 5ee13fb add test.txt
* 8adc53b understand how stage works
* df04479 append GPL
* ceb257f add a word --- distributed
* efc5467 wrote a readme file
```
不使⽤用“Fast forward”模式，merge后就像这样


###4.3.2 分支策略---master分支、dev分支、个人分支
1. master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
2. 干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，⽐比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；
3. 每个工作人员都在dev分支上干活，每个人都有自己的分支，时不时地往dev分支上合并
团队合作的分支看起来就像这样:

##4.4 bug分支
在Git中，由于分支是如此的强大，所以每个bug都可以通过一个新的临时分支来修复，修复后，合并分支，然后将临时分支删除。
场景：
1. 你正在dev上进行的工作进行到一半, 预计还需1天才能完成, 不能提交
2. 你接到一个修复bug-101的任务, 要在2小时内修复.
怎么办?
Git提供了一个<font color=red>stash功能</font>，可以把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
```Bash
# ①把当前工作现场储藏起来
$ git stash
Saved working directory and index state WIP on master: 4c312eb merge with no-ff
HEAD is now at 4c312eb merge with no-ff
# 查看工作区，是干净的, 可以放心地创建分支来修复bug。
$ git status
On branch master
nothing to commit, working tree clean

# ②⾸建立临时分支, 修复bug, 提交
# 先确定要在哪个分支上修复bug(假定在master分支上修复)，从master创建临时分支
$ git checkout master
$ git checkout -b issue-101
Switched to a new branch 'issue-101'
# 修复bug-101
$ git add readme.txt
$ git commit -m "fix bug-101"
[issue-101 f2ab95b] fix bug-101
 1 file changed, 1 insertion(+)

# ③ 修复完成后，切换到master分支，并完成合并，最后删除issue-101分支
$ git checkout master
Switched to branch 'master'
$ git merge --no-ff -m "merge fix bug-101" issue-101
Merge made by the 'recursive' strategy.
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
$ git branch -d issue-101
Deleted branch issue-101 (was f2ab95b).

# ④ 回到dev分支接着干活. 工作区是干净的
$ git checkout dev
Switched to branch 'dev'

# ⑤ 恢复工作现场
$ git stash list  # 查看工作现场列表. 工作现场还在
stash@{0}: WIP on dev: 4c312eb merge with no-ff

# 恢复工作现场有2种办法:
# 1. git stash apply; git stash drop 先恢复, 再删除
# 2. git stash pop 恢复 & 删除
# 多次stash后, 恢复指定的stash: git stash apply stash@{0}
$ git stash pop
On branch master
Your branch is ahead of 'origin/master' by 6 commits.
  (use "git push" to publish your local commits)
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
        modified:   readme.txt
no changes added to commit (use "git add" and/or "git commit -a")
Dropped refs/stash@{0} (c0cbe2b234fb6e5b45ae086cc2f571600bb438ce) 
```

##4.5 Feature分支(新功能实验分支)
添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了
所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后合并，最后再删除该feature分支。
```Bash
$ git checkout -b feature-wifi
# 开发新功能, add & commit
$ git add wifi.c ; git commit -m "add feature wifi"

# 切回dev，准备合并
$ git checkout dev

# 就在此时，接到上级命令，因经费不⾜足，新功能必须取消
$ git branch -d feature-wifi
error: The branch 'feature-wifi' is not fully merged.
If you are sure you want to delete it, run 'git branch -D feature-wifi'.
# Git友情提醒，feature-vulcan分支还没有被合并，如果要强行删除，需要使用命令git branch -D feature-vulcan
$ git branch -D feature-wifi
Deleted branch feature-wifi (was 0a51ec8).
```

##4.6 多人协作
当你从远程仓库克隆时，实际上Git自动把本地的master分支和远程的master分支对应起来
了，并且远程仓库的默认名称是origin。
###4.6.1 查看远程库的信息
```Bash
$ git remote
origin
$ git remote -v #更详细
origin  https://github.com/yaksazhu/git_test_2016.git (fetch)
origin  https://github.com/yaksazhu/git_test_2016.git (push)
```

###4.6.2 推送分支
```Bash
$ git push origin dev
Username for 'https://github.com': yaksazhu
Counting objects: 4, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 427 bytes | 0 bytes/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local objects.
To https://github.com/yaksazhu/git_test_2016.git
 * [new branch]      dev -> dev
```
并不是一定要把本地分支往远程推送，那么哪些分支需要推送，哪些不需要呢？
• master分支是主分支，因此要时刻与远程同步；
• dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
• bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
• feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

###4.6.3 抓取分支
```Bash
$ git clone git@github.com:michaelliao/learngit.git

#从远程库clone时，默认情况下，只能看到本地的master分支。
#要在dev分支上开发，就必须创建远程origin的dev分支到本地
$ git checkout -b dev origin/dev
#把dev分支push到远程
$ git commit -m "add xxxx"
$ git push origin dev
```

###4.6.4 推送冲突
如果你的合作者已经向origin/dev分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送, 会导致推送失败
```Bash
$ git add xxxx
$ git commit -m "add xxxx"
$ git push origin dev
To git@github.com:michaelliao/learngit.git
 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:michaelliao/learngit.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Merge the remote changes (e.g. 'git pull')
hint: before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
解决办法: 先用git pull把最新的提交从origin/dev抓下来，然后，在本地合并，解决冲突，再推送
```Bash
# ① 把别人做的最新的提交从origin/dev抓下来
$ git pull
#git pull也失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接
#设置dev和origin/dev的链接
$ git branch --set-upstream dev origin/dev
Branch dev set up to track remote branch dev from origin.
#再pull
$ git pull
Auto-merging hello.py
CONFLICT (content): Merge conflict in hello.py
Automatic merge failed; fix conflicts and then commit the result.
这回git pull成功，但是合并有冲突，需要手动解决，解决的方法和分支管理中的解决冲突完全一样。

# ② 解决后，提交，再push
$ git commit -m "merge & fix hello.py"
[dev adca45d] merge & fix hello.py
$ git push origin dev
Counting objects: 10, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 747 bytes, done.
Total 6 (delta 0), reused 0 (delta 0)
To git@github.com:michaelliao/learngit.git
   291bea8..adca45d  dev -> dev
```

多人协作的工作模式通常是这样：
1. 首先，用git push origin branch-name推送自己的修改；
2. 如失败，是因为远程分支比你的本地更新，需要先用git pull试图合并；
3. 如合并有冲突，则解决冲突，并在本地提交；
4. 解决掉冲突后，再用git push origin branch-name推送就能成功！

#5. 标签管理
发布一个版本时，我们通常先在版本库中打一个标签，这样，就唯一确定了打标签时刻的版本。
将来⽆无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。
所以，标签也是版本库的一个快照。
Git的标签虽然是版本库的快照，但其实它就是指向某个commit的指针，所以，创建和删除标签都是瞬间完成的。
（跟分支很像对不对？但是分支可以移动，标签不能移动）
##5.1 创建标签
```Bash
# ① 切换到需要打标签的分支上
$ git checkout master
$ git tag v1.00    #打一个新标签 (在最新commit处)
$ git tag          #查看所有标签
v1.00
$ git tag v0.90 783ca48 #在以前commit的ID处打标签

# 创建带有说明的标签，-a指定标签名，-m指定说明文字
$ git tag -a v0.01 -m "最初版" efc5467

# 还可以通过-s用私钥签名一个标签 (签名采用PGP签名，因此必须首先安装gpg (GnuPG) )
$ git tag -s v0.2 -m "signed version 0.2 released" fec145a
#⽤用PGP签名的标签是不可伪造的，因为可以验证PGP签名。

# 注意，标签不是按时间顺序列出，⽽而是按字母排序的。
# 可以用git show tagname查看标签信息
$ git show v1.00
commit 4c312eb8cfa5abeca4e4d4c79f6e812aa927776d
Merge: d86daa2 783ca48
Author: Roger Zhu <yaksazhu@163.com>
Date:   Thu Sep 22 10:06:39 2016 +0800
    merge with no-ff 
```
##5.2 操作标签
```Bash
# 删除标签
$ git tag -d v0.90
Deleted tag 'v0.90' (was 783ca48)
#因为创建的标签都只存储在本地，不会自动推送到远程。所以打错的标签可以在本地安全删除

# 推送tag到远程
$ git push origin v1.00
$ git push origin --tags  #一次性推送全部尚未推送的本地标签

# 如果标签已经推送到远程, 删除起来就麻烦一点
# 先从本地删除, 再从远程删除
$ git tag -d v0.01
Deleted tag 'v0.01' (was 6bbed3d)
$ git push origin :refs/tags/v0.01
To github.com:yaksazhu/git_test_2016.git
 - [deleted]         v0.01
```
要看看是否真的从远程库删除了标签，可以登陆GitHub查看

#6. 使用GitHub
其实GitHub还是一个开源协作社区，通过GitHub，既可以让别人参与你的开源项目，也可以参与别人的开源项目。
在GitHub出现以前，开源项目开源容易，但让⼲广大人民群众参与进来比较困难，因为要参与，就要提交代码，而给每个想提交代码的群众都开一个账号那是不现实的，因此群众也仅限于报个bug，即使能改掉bug，也只能把diff文件用邮件发过去，很不方便。
但是在GitHub上，利用Git极其强大的克隆和分支功能，人们真正可以自由参与各种开源项目了。

##6.1 如何参与一个开源项目呢？
访问一个项目的主页，点Fork就在自己的账号下克隆了一个仓库，然后从自己的账号下clone到本地
```Bash
git clone git@github.com:michaelliao/bootstrap.git
```
一定要从自己的账号下clone仓库，这样你才能推送修改。如果从bootstrap的作者的仓库
地址git@github.com:twbs/bootstrap.git克隆，因为没有权限，你将不能推送修改。
官方仓库、你在GitHub上克隆的仓库、以及你自己克隆到本地的仓库，它们的关系如下图所示：

你修复了一个bug，或者新增了一个功能，可以往自己的仓库推送。
如果你希望官方库能接受你的修改，你就可以在GitHub上发起一个pull request。

#7. 自定义Git
##7.1 让Git显示颜色
`$ git config --global color.ui true`

##7.2 忽略特殊文件(不需提交的文件)
在Git工作区的根目录下创建一个`.gitignore`文件，然后把要忽略的文件名填进去，再把它也提交，Git就会自动忽略这些文件。
不需要从头写`.gitignore`文件，GitHub已经为我们准备了各种配置文件，只需要组合一下就可以使用了。
所有配置文件可以直接在线浏览：https://github.com/github/gitignore

忽略文件的原则是：
1. 忽略操作系统自动生成的文件，如缩略图等；
2. 忽略编译生成的中间文件、可执行文件等，如Java编译产生的.class文件；
3. 忽略你自己的带有敏感信息的配置文件，如存放口令的配置文件。
如:
```ini
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini
# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build
# My configurations:
db.ini
deploy_key_rsa
```
##7.3 配置别名
```Bash
$ git config --global alias.st status  # st就代表了status
$ git config --global alias.co checkout
$ git config --global alias.cm commit
$ git config --global alias.br branch
$ git config --global alias.unstage 'reset HEAD' #撤销修改
$ git config --global alias.last 'log -1' #显示最近一次的提交

$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```


#8. 搭建Git服务器
既不想公开源代码，又舍不得给GitHub交保护费，那就只能自己搭建一台Git服务器作为私有仓库使用。

##8.1 搭建Git服务器
```Bash
# 第一步，安装git：
$ sudo apt-get install git

# 第二步，创建一个git用户，用来运行git服务：
$ sudo adduser git

# 第三步，创建证书登录：
# 收集所有需要登录的用户的公钥---id_rsa.pub文件，把所有公钥导入到 /home/git/.ssh/authorized_keys文件里，一行一个。

# 第四步，初始化Git仓库：
# 先选定一个目录作为Git仓库，假定是/srv/sample.git，在/srv目录下输入命令：
$ sudo git init --bare sample.git
# Git就会创建一个裸仓库，裸仓库没有工作区，因为服务器上的Git仓库纯粹是为了共享，所以不让用户直接登录到服务器上去改工作区，并且服务器上的Git仓库通常都以.git结尾。
# 然后，把owner改为git：
$ sudo chown -R git:git sample.git

# 第五步，禁用shell登录：
# 出于安全考虑，git用户不允许登录shell，这可以通过编辑/etc/passwd文件完成。找到类似下⾯面的一行：
# git:x:1001:1001:,,,:/home/git:/bin/bash
# 改为：
# git:x:1001:1001:,,,:/home/git:/usr/bin/git-shell
# 这样，git用户可以正常通过ssh使用git，但无法登录shell，因为我们为git用户指定的git-shell每次一登录就自动退出。

# 第六步，终端用户在自己的电脑上克隆远程仓库：
$ git clone git@server:/srv/sample.git
Cloning into 'sample'...
warning: You appear to have cloned an empty repository.

# 剩下的推送就简单了。
```

##8.2 管理公钥
如果团队很小，把每个人的公钥收集起来放到服务器的/home/git/.ssh/authorized_keys文件里就是可行的。
如果团队有几百号人，就没法这么玩了，这时，可以用Gitosis来管理公钥。
这里我们不介绍怎么玩 Gitosis了，几百号人的团队基本都在500强了，相信找个高水平的Linux管理员问题不大。

##8.3 管理权限
有很多不但视源代码如生命，而且视员工为窃贼的公司，会在版本控制系统里设置一套完善的权限控制，每个人是否有读写权限会精确到每个分支甚至每个目录下。
因为Git是为Linux源代码托管而开发的，所以Git也继承了开源社区的精神，不支持权限控制。
不过，因为Git支持钩⼦子（hook），所以，可以在服务器端编写一系列脚本来控制提交等操作，达到权限控制的目的。Gitolite就是这个工具。
这里我们也不介绍Gitolite了，不要把有限的生命浪费到权限斗争中。
