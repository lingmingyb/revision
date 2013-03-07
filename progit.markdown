Progit Notes
============

第1章：起步
------------

### Git基础要点 ###

**设计哲学**

* 传统VCS系统保存项目前后不同版本各个文件间的Patch, 然后根据这些Patch计算出
  各个版本的文件内容。
  Git并不保存这些前后变化的差异数据(patch), 而是直接对变化后的文件做
  快照，并保存一个指向这次快照的索引。
* Git的绝大数操作都只需要访问本地文件和资源，而CVS差不多每次操作都需要
  连接网络。
* 如果要查看当前版本的文件和一个月前的版本之间有何差异，Git会取出一个月
  前的快照和当前文件做一次差异运算。
* 时刻保存数据完整性。每次保存之前，Git都会对所有数据进行内容的校验和(checksum)
  计算，并将结果(指纹字符串)作为数据的唯一标识和索引。换句话说，不可能在修改了目录
  或文件内容后，Git一无所知。这项特性作为Git的设计哲学，建在整体架构的最底层。

  

**三种状态**

对于任何一个文件，在Git内部都只有三种状态，已修改(modified), 已暂存(staged)
和已提交(commited)。

从项目中取出某个版本的所有文件和目录，用以开始后续工作的叫做工作目录。
所谓暂存区不过是个简单的文件，含有下一次提交将要发生的内容的信息。

基本的Git工作流程如下所示：

1. 修改：在工作目录中修改某些文件;
2. 暂存：对这些修改了的文件做快照,并将其保存到暂存区域;
3. 提交：提交更新，将保存在暂存区的文件快照永久储存到Git目录中。

**配置Git**

	$ git config --system user.name		`/etc/gitconfig`
	$ git config --global user.name		`~/.gitconfig`
	$ git config ???	  user.name		`.git/config`

**获取帮助**

	$ git help <command>
	$ git <command> --help
	$ man git-<command>


第2章：Git基础
--------------

### 2.1 取得项目的Git仓库 ###

**在项目目录初始化**

	$ cd ~/Notes
	$ git init

**从现有项目克隆**

	$ git clone git://url/to/.git [mygitrepo_name]

现在我们已经有了一个真实项目的Git仓库，并且从仓库中取出了最新版本
的文件拷贝。

工作目录下的所有文件不外乎这两种状态：已跟踪或未跟踪。初次克隆某个
仓库时，工作目录所有的文件都属于已跟踪文件，而且状态属于未更改。而
在一个已存在目录内初始化Git仓库后，工作目录所有文件都属于未跟踪。

### 2.2 记录每次更新到仓库 ###

**检查当前文件状态**
	
	$ git status

**跟踪新文件**

	$ mv ~/Progit.md Noets/
	$ git add Progit.md

**暂存已修改文件**

	$ git add Progit.md

(`git add`是个多功能命令，根据目标文件状态的不同，此命令的效果也不同：
可以用它开始跟踪新文件，或者把已跟踪并修改的文件放入暂存区，还能用于
合并时把有冲突的文件标记为已解决状态等。)

**忽略某些文件**

	$ cat .gitignore

	# 该行为注释
	*.swp

**查看已暂存和未暂存的更新**

	$ git diff

查看哪些更新还没有暂存。

	$ git diff --staged

或者
	
	$ git diff --cached

查看哪些更新已经暂存起来等待下次提交。

**提交更新**

	$ git commit -m "the very first commit"

**跳过使用暂存区域**

尽管使用暂存区域可以精心地准备要提交的细节，可有时候略显繁琐。
在提交的时候，给`git commit`加上`-a`选项，Git就会把所有已跟踪并
更改了的文件暂存起来一并提交，从而跳过`git add`步骤。

	$ git commit -a 

**移除文件**

1. 工作目录先删除一个文件，提交这一变化至Git仓库，流程跟添加一个
新文件是一致的。

		$ rm README		#删除目录上的文件(对应目录内创建新文件)
		$ git rm README		#解除跟踪(对应`git add`的添加跟踪)
		$ git commit  	#提交“解除跟踪”

2. 如果一个新跟踪文件，`git rm README`:

	>error: README has changes staged in the index  
	>(use --cached to keep the file, or -f to force removal)

		$ git rm --cached README
		
		$ git rm -f README

3. 如果一个文件已在Git仓库里被跟踪且未修改:

		$ git rm README
		$ git commit 

		$ git rm --cached README
		$ git commit

4. 如果一个文件已在Git仓库里被跟踪，已修改但未暂存，`git rm README`：
	
	>error: README has local modifications  
	>(use --cached to keep the file, or -f to force removal)

		$ git rm --cached README
		$ git commit

		$ git rm -f README
		$ git commit

5. 如果一个文件已在Git仓库里被跟踪，已修改也已暂存，`git rm README`:

	>error: README has changes staged in the index  
	>(use --cached to keep the file, or -f to force removal)

		$ git rm --cached README
		$ git commit

		$ git rm -f README
		$ git commit

**移动文件**

给被Git跟踪的某个文件重命名：
	
	$ git mv README.txt README
	$ git commit

实际上相当于运行了下面三个命令：
	
	$ mv README.txt README
	$ git rm README.txt
	$ git add README
	$ git commit

### 2.3 查看提交历史 ###

	$ git log [arguments]

	$ git log -<n>
	$ git log --pretty=[arg]
	$ git log --pretty=format
	$ git log -p
	$ git log --author --commiter --all-match
	$ git log --since="date" --before="date"  --<file>

### 2.4 撤销操作 ###

**修改最近一次提交**

	$ git commit --amend

此命令将使用当前的暂存快照提交，并且有机会修改提交说明。

如果上次提交时忘了暂存某些修改，可以先不上暂存操作，然后再`--amend`提交：

	$ git commmit -m 'yet another commit'
	$ git add forgotten_file
	$ git commit --amend

**取消已经暂存的文件**

	$ git reset HEAD <file>

**取消对文件的修改**

	$ git checkout -- <file> 

这条命令有些危险，所有对文件`file`刚才的修改都没了。如果只是想要回退版本，同时
保留刚才的修改以便将来继续工作，可以使用下章介绍的stashing和分支来处理。

记住，任何提交到Git的数据，都可以被恢复。可能失去的数据仅限于没有提交果的，
对Git来说它们就像从未存在过一样。

### 2.5 远程仓库的使用 ###

**查看当前的远程库**
		
	$ git remote [-v]

在克隆完某个项目后，至少可以看到一个名为origin的远程库，Git默认使用这个名字
来标识你所克隆的原始仓库。

**添加远程仓库**
	
	$ git remote add [remote-name] [url]

**从远程仓库抓取数据**

	$ git fetch [remote-name]

该命令回到远程仓库中拉取所有你本地仓库中还没有的数据。运行完成后，你就可以在
本地访问该远程仓库中的所有分支，将其中某个分支合并到本地，或者只是取出某个分支
一探究竟。

`fetch`命令只是将远端的数据拉到本地仓库，并不自动合并到当前分支，当准备好的时候
可以手动合并。而`pull`命令则将远端分支自动合并到工作目录中当前分支。

**推送数据到远程仓库**

	$ git push [remote-name] [branch-name]

如果要把本地的`master`分支推送到`origin`服务器上(`git clone`操作会自动使用
`master`和`origin`名字), 可以运行下面这个命令：

	$ git push origin master

只有在克隆的服务器上有写权限，或者同一时刻没有人在推数据，这条命令才可如期
完成任务。如果你在推数据前，已有人推送了若干更新，那你的推送操作就会被驳回。
你必须先把它们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送。

**查看远程仓库信息**

我们可以通过命令
	
	$ git remote show [remote-name]

来查看某个仓库的远程信息(需要连接网络)。比如要查看所克隆的远程仓库，可以运行：

	$ git remote show origin

**远程仓库的删除和重命名**

	$ git remote rename pb paul

	$ git remote rm paul

### 2.6 打标签 ###

**列显已有的标签**

	$ git tag

**新建标签**

Git使用的标签有两种类型：轻量级的(lightweight)和含附注的(annotated). 轻量级
标签就是个指向某次特定提交的引用(指针);而含附注标签则是储存在仓库中的一个独立对象，
它有自身的校验和信息，包含着打标签人的名字，电子邮件和打标签日期，以及标签说明，
标签本身也允许使用GNU Privacy Guard(GPG)来签署和验证。

**含附注的标签**

	$ git tag -a v0.1 -m 'my version 0.1'

	$ git show v0.1

	$ git tag -s v0.2 -m 'my signed version 0.2'

**轻量级标签**

	$ git tag v0.1-lw
	$ git show v0.1-lw

**验证标签**

	$ git tag -v [tag-name]

此命令会条用GPG来验证签名，所以你需要有签署者的公钥，存放在keyring中才能验证。

**后期加注标签**
	
可以在后期对早先的某次提交加注：

	$ git log pretty=oneline

	$ git tag -a v1.2 9fceb02

	$ git tag

	$ git show v1.2

**分享标签**

默认情况下，`git push`并不会把标签传送到远程服务器上，只有通过显示命令才能分享
标签到远端仓库。其命令格式如同推送分支：

	$ git push origin [tag-name]

如果要一次推送所有(本地新增的)标签上去，可以使用`--tags`选项：

	$ git push origin --tags

现在，其他人克隆共享仓库或拉取数据同步后，也会看到这些标签。

### 2.7 技巧和窍门 ###

**自动补全**

	$ cp git-src/contrib/completion/git-completion.bash ~/.git-completion.bash

	$ echo "source ~/.git-completion.bash" >> ~/.bashrc

或者系统级别启用：

	$ cp git-src/contrib/completion/git-completion.bash /etc/bash_completion.d/
	
**Git别名**

	$ git config --global alias.co checkout
	$ git config --global alias.br branch
	$ git config --global alias.ci commit
	$ git config --global alias.st status
	$ git config --global alias.unstage 'reset HEAD --'
	$ git config --global alias.last 'log -1 HEAD'
	$ git config --global alias.visual "!gitk"


第3章：Git分支
--------------

### 3.1 何谓分支 ###

几乎每一种版本控制系统都以某种形式支持分支。使用分支意味着你可以从开发主线上分离
开来，然后在不影响主线的同时继续开展自己的工作。

在Git提交时，会在Git仓库里保存三种对象：

* blob对象 - 分别保存暂存区各个文件内容;
* tree对象 - 保存暂存区各个文件与blob对象的对应索引;
* commit对象 - 保存父`commit对象`索引，`tree对象`索引和提交信息元数据。

假设暂存区有三个文件，那么提交后仓库里会多出5个对象(三个blob对象，一个tree对象和
一个commit对象). 如果做些修改后再次提交，那么这次的commit对象里会包含一个指向
上次commit对象的指针。

**Git分支**

Git中的分支，其实本质上仅仅是个指向某个`commit对象`的可移动指针。Git会使用master
作为分支的缺省名字。在若干次提交后，你其实已经有了一个指向最后一次`commit对象`的
master分支，它在每次提交的时候都会自动向前移动。

**创建分支**

那么在Git里，创建一个新的分支就是创建一个新的分支指针。比如创建一个`testing`分支：

	$ git branch testing

这会在当前`commit对象`上新建一个分支指针(此时共有`master`和`testing`两个分支指针
指着当前`commit对象`)。

**切换分支**

Git保存着一个名为HEAD的特别指针，它指向那个分支指针，那么我们就在哪个分支上。
要切换Git分支：

	$ git checkout testing

这样HEAD就指向了`testing`分支。

现在我们可以在`testing`分支任意提交数据，而`master`分支仍然指着切换分支时所指着的
那个`commit对象`。

我们随时可以用

	$ git checkout master

命令回到`master分支`，这条命令做了两件事。它把HEAD指针移回到`master`分支，并把
工作目录中的文件换成了`master`分支所指向的快照内容。

我们可以在不同分支里来回切换，各自开发，所做的改变都孤立在各自的分支里，可以在
时机成熟的时候将这些改变合并到一起。

由于Git中的分支仅仅是一个包含所指`commit对象`校验和(40个字符长度SHA-1字串)的文件，
所以创建和销毁一个分支就变得非常廉价。

同时，因为每次提交都记录了父`commit对象`的信息，所以以后要合并分支时，寻找合适的
合并基础(`merge base`, 即相同的父`commit对象`)的工作已经自动完成。

### 3.2 基本的分支与合并 ###

**应用场景举例**

应用到简单的分支与合并工作流的例子：

1. 开发一个网站。
2. 为实现某个新的需求，创建一个分支。
3. 在这个新分支上开展工作。

假设此时，突然接到一个电话说有一个很严重的问题需要修补，那么可按照下面的方式处理：

1. 返回到原先已经发布到生产服务器上的分支。
2. 为这次紧急修补建立一个分支。
3. 测试通过后，将此修补分支合并，再推送到生产服务器山。
4. 切换到之前实现新需求的分支，继续工作。

**基本分支**

现在，为实现这个新的需求，创建一个分支`new_feature`:

	$ git checkout -b new_feature

相当于

	$ git branch new_feature
	$ git checkout new_feature

正在此分支上工作，突然电话打来需要修复生产服务器上某个问题，回到master分支：

	$ git checkout master

(在转换分支之前，留心暂存区或工作目录里那些还没提交的修改，它会和你即将检出的分支
产生冲突而阻止Git为你转换分支). 转换分支的时候最好保持一个清洁的工作目录。稍后会
介绍几个绕过这种问题的方法(分别叫做`stashing`和`amending`).

切换分支后，有一点值得牢记：Git会把工作目录的内容恢复成检出某分支时它所指向的那个
commit快照。它会自动添加、删除和修改文件以确保目录的内容和上次提交时一模一样。

接下来我们创建一个紧急修补分支来开展工作，直到搞定：

	$ git checkout -b hotfix

有必要做些测试，确保修补是成功的，然后把它合并到master分支并发布到生产服务器。

	$ git checkout master
	$ git merge hotfix		# Fast forward

现在`master`分支和`hotfix`分支指向相同的`commit对象`，现在可以删掉`hotfix`分支：

	$ git branch -d hotfix

现在可以回到`new_feature`分支上继续工作：

	$ git checkout new_feature

现在两个分支的情况是这样的：

				       (master)
					 	  |
						  |
	[c0]<--[c1]<--[c2]<--[c4]
					\
					 *<--[c3]<--[c5]
					  			 |
								 |
							(new_feature)

不要担心hotfix的内容还没包含在`new_feature`中。如果确实需要纳入此次修补，可以用
`git merge master`把`master`分支合并到`new_feature`，或者等完成后，将`new_feature`
分支中的更新并入`master`。
					 
**基本合并**

在`new_feature`的工作完成之后，可以合并回master分支：

	$ git checkout master
	$ git merge new_feature

现在分支情况是这样的：

				       				   (master)
					 	  				  |
					 					  |
									 (snapshot to 
			(common ancestor)		  merge into)
					|				      |
	[c0]<--[c1]<--[c2]<--[c4]<----------[c6]
					\				    /	
					 *<--[c3]<--[c5]<--*
					 			 |
							(snapshot to
							  merge in)
					  			 |
								 |
							(new_feature)

这次合并的实现，并不同于之前hotfix的并入方式。这一次，你的开发历史是从更早的地方
开始分叉的。由于当前`master`分支所指向的`commit[c4]`并非想要并入分支`new_feature`
的直接祖先，因此Git不得不进行一些处理。此时，Git会用两个分支的末端([c4]和[c5])和
它们的共同祖先[c2]进行一次简单的三方合并计算。

Git没有简单地把分支指针右移，而是对三方合并的结果作一新的快照，并自动创建一个指向
它的commit([c6])。我们把这个特殊的commit称作合并提交(merge commit), 因为它的祖先
不止一个。

值得一提的是Git可以自己裁决哪个共同祖先才是最佳合并基础，所以此特性让Git的合并操作
比其它系统要简单不少。

既然工作结果已经合并了，`new_feature`分支也就没有用了。删除之：

	$ git branch -d new_feature


**冲突的合并**

有时候合并操作并不会如此顺利。如果你修改了两个待合并分支里同一个文件的同一个部分，
Git就无法干净地把两者合到一起。如果你在`new_feature`的开发过程中修改了hotfix中修
改的部分，合并将产生冲突，只能由人手动merge那个文件。

可以用`git status`查看哪些文件合并发生了冲突。任何包含未解决冲突的文件都会以未合
并(`unmerged`)状态列出。

	$ git status

此时可以选取合并工具来合并冲突文件：

	$ git mergetool

在解决了所有文件的冲突以后，将它们标记为已解决：

	$ git add <the_file>

最后提交：

	$ git commit -m 'the conflicts merge information'

提交信息应该含有冲突合并细节。


### 3.3 分支管理 ###


列出所有分支，带`*`的为当前所在分支:

	$ git branch [-v]

查看哪些分支已经(或尚未)与当前分支合并：

	$ git branch --merged
	$ git branch --no-merged

合并过的分支可以用

	$ git branch -d <branch-name>

删除掉。未与当前分支合并的分支不可以用`-d`删除：

	$ git branch -d testing

> error: The branch 'testing' is not an ancestor of your current HEAD.


### 3.4 分支式工作流程 ###

**长期分支**

	(master)[c1]---------------------------------->
		\
	 (develop)[c2]<---[c3]-------------------->
					    \
					 (topic)[c4]<---[c5]------>

可以用这种方式维护不同层次的稳定性。

**特性分支**



											<--[c12]<--[c13] (dumbidea)
										   /
	(master)[c0]<--[c1]<--[c3]<--[c9]<--[c10]
					 \
					  *<--[c2]<--[c4]<--[c5]<--[c6] (iss91)
					           	   \
								    *<--[c7]<--[c8]<--[c11] (iss91v2) 

提交到[c1]的时候，从`master`分出`iss91`分支来解决#91号问题。在该分支提交到[c6]
时有了一个更好的修补方法，便从[c4]开始另分出一个分支`iss91v2`来解决#91号问题。
同时在`master`分支提交到[c10]的时候，项目有了一个新想法，便分出分支`dumbidea`来
支持该想法。到最后，发现`dumbidea`和`iss91v2`都非常好，便舍弃`iss91`分支的[c5]
和[c6]，将这两个完成的分支并入到`master`分支。

合并以后提交历史变成这样：


											  (dumbidea)
												  |
	[c0]<--[c1]<--[c3]<--[c9]<--[c10]<--[c12]<--[c13]<--[c14] (master)
			\											  /
			 *<--[c2]<--[c4]<--[c7]<--[c8]<--[c11]<------*
			 								   |
											(iss91v2)
											
### 3.5 远程分支 ###

远程分支(remote branch)是对远程仓库状态的索引。它们是一些无法移动的本地分支，只
有在进行Git的网络活动时才会更新。远程分支就相当于书签，提醒着你上次连接远程仓库时
上边各个分支的位置。

我们用“远程仓库名/分支名”这样的形式表示远程分支。

一次Git克隆会建立你自己的本地分支`master`和远程分支`origin/master`，它们都指向
`orgin/master`分支的最后一次提交。

		     (origin/master)  <== remote branch
			 		|
	[c1]<--[c2]<--[c3]
			 		|
				(master)	  <== local branch

要是你在本地`master`分支做了会儿事情，与此同时，其他人向该例中的远程仓库推送了
内容，更新了上面的`master`分支，那么你的提交历史会开始朝不同方向发展。不过只要你
不和服务器通讯，你的`origin/master`指针不会移动。

可以运行`git fetch orgin`命令来进行同步。该命令首先找到`origin`是哪个服务器，从
上面获取你尚未拥有的数据，更新你本地的数据库，然后把`origin/master`的指针移动到
它最新的位置。

**推送**

要想和其它人分享某个分支，你需要把它推送到一个你拥有写权限的远程仓库。你的本地
分支不会被自动同步到你引入的远程分支中，除非你明确执行推送操作。换句话说，对于
无意分享的，你尽可以保留为私人分支，而只推送哪些协同工作的特性分支。

如果你有个叫`serverfix`的分支需要与别人协同开发，可以运行：

	$ git push origin serverfix

这其实是简便写法。Git自动把`serverfix`分支名扩展为`refs/heads/serverfix:refs/
heads/serverfix`，意味取出我的`serverfix`本地分支，推送它来更新远程仓库的`serverfix`
分支。(以后将介绍`/refs/heads/`细节)，不过一般使用的时候可以省略它。

也可以这样写：

	$ git push origin serverfix:serverfix

还可以给远程分支起个跟本地分支不同的名字：

	$ git push origin serverfix:awesomebranch

接下来，当你的协作者再次从服务器上获取数据时，他们将得到一个新的远程分支`origin/
serverfix`.

	$ git fetch orgin

> ...  
> From git@github.com:kdlijian/...  
>  [new branch]		serverfix  ->  origin/serverfix

值得注意的是，在`fetch`操作(按：不是`clone`)抓来某些远程分支之后，你得不到相应的
可编辑的本地分支。比如，在本例中，你的合作者`fetch`到了一个新远程分支`serverfix`，
他们并不会得到一个本地分支`serverfix`，有的只是一个无法移动的`original/serverfix`
分支指针。

如果要把该内容合并到当前分支，可以运行:

	$ git merge origin/serverfix

如果他们想要一份自己的`serverfix`来开发，可以在远程分支的基础上分化出一个新的分支来：

	$ git checkout -b serverfix origin/serverfix

这会切换到新建的`serverfix`本地分支，其内容同远程分支`origin/serverfix`一致，你可以
在里面继续开发了。

**跟踪分支**

从远程分支检出(分化出，见上例)的本地分支叫做跟踪分支(tracking brach)。跟踪分支是一
种和远程分支有直接联系的本地分支。在跟踪分支里输入`git push`, Git会自行判断向哪个
服务器的哪个分支推送数据;反过来，在这些分支里运行`git pull`会获取所有远程索引，并
把他们的数据都合并到本地分支中来。

在克隆仓库时，Git通常会自动建立一个`master`分支来跟踪`origin/master`。这正是`git 
push`和`git pull`一开始就能正常工作的原因。当然，也可以设定其它跟踪分支：

	$ git checkout -b [分支名] [远程仓库名]/[分支名]

或者

	$ git checkout --track origin/serverfix

可以改变名字：

	$ git checkout -b sf origin/serverfix

现在你的本地分支`sf`会自动向`origin/serverfix`抓取和推送数据了。

**删除远程分支**

如果不再需要某个远程分支了，比如搞定了某个特性并把它合并到了远程的`master`分支里，
可以用这个无厘头的语法来删除它：

	$ git push origin :serverfix

意味“在这里提取空白然后把它变成远程分支”，即删除远程分支。


### 3.6 衍合 ###

把一个分支整合到另一个分支的办法有两种，`merge(合并)`和`rebase(衍合)`。

**衍合基础** 

						  
					 <--[c3] (experiment)
					/
	[c0]<--[c1]<--[c2]<--[c4] (master)

之前介绍过，最容易的整合分支的方法是`merge`命令，它会把两个分支最新的快照([c3]和
[c4])以及二者共同的祖先([c2])进行三方合并。合并的结果作一快照提交为[c5]:

					 <--[c3]<---
					/			\
	[c0]<--[c1]<--[c2]<--[c4]<---[c5]

其实，还有另外一个选择：你可以把在[c3]里产生的变化补丁重新在[c4]的基础上打一遍。
在Git里，这种操作叫做衍合(rebasing)。有了`rebase`命令，就可以把在一个分支里提交的
改变在另一个分支里重演一遍。

在这个例子里，可以运行下面的命令：

	$ git checkout experiment
	$ git rebase master

它的原理是回到两个分支(你所在分支`experiement`和你想要衍合入的分支`master`)的共同
祖先([c2]),提取你所在分支(`experiment`)每次提交时产生的差异(diff),把这些差异分别保存
到临时文件里，然后从当前分支转换到你需要衍合入的分支(`master`)，(在[c4]基础上)依序
施用每一个差异补丁文件。

结果下图所示：

							 (experiment)
								  |
								  |
	[c0]<--[c1]<--[c2]<--[c4]<--[c3']
						  |
						  |
					   (master)


现在，回到`master`分支然后进行一次快进合并。

	$ git checkout master
	$ git merge experiment

							 (experiment)
								  |
								  |
	[c0]<--[c1]<--[c2]<--[c4]<--[c3']
								  |
								  |
							   (master)

合并以后，[c3']所指的快照，同三方合并例子中的[c5]所指的快照内容一模一样了。最后
整合得到的结果没有任何区别，但衍合能产生一个更为整洁的提交历史。如果视察一个衍合
过的分支的历史记录，看起来更清楚：仿佛所有修改都是先后进行的，尽管实际上他们原来
是同时发生的。

(按：总的来说，衍合就是把本不能快进的合并变成快进合并。请在有需要用的时候再使用。)

**更多有趣的衍合**


	[c1]<--[c2]<--[c5]<--[c6]	(master)
			 \
			  *<--[c3]<--[c4]<--[c10]	(server)
			  		\
					 *<--[c8]<--[c9]	(client)

(从一个特性分支里再分出一个特性分支的历史)

如图，你创建了一个特性分支`server`来给服务器端代码添加一些功能，然后提交c3和c4。
然后从c3的地方再增加一个`client`分支来对客户端代码进行一些修改，提交c8和c9。最后
又回到`server`分支提交了c10。

假设在接下来的一次软件发布中，你决定把客户端的修改先合并到主线中，而暂缓并入服务器
端软件的修改(因为还需要进一步测试)。你可以仅提取对客户端的改变(c8和c9)，然后通过
`git rebase`的`--onto`选项来把它们在`master`分支上重演。

	$ git rebase --onto master server client

这基本上是说“检出`client`分支，找出`client`分支和`server`分支的共同祖先之后的变化
(即c8和c9)，然后把它们在`master`上重演一遍。

现在可以快进`master`分支了：

	$ git checkout master
	$ git merge client

结果如下图所示：

	[c1]<--[c2]<--[c5]<--[c6]<--[c8']<--[c9']	(master)
		     \
			  *<--[c3]<--[c4]<--[c10]	(server)

现在你决定把`server`分支的变化也包含进来。可以直接把`server`分支衍合到`master`而
不用手动转到`server`分支再衍合。`git rebase [主分支] [特性分支]`命令会先检出特性
分支`server`，然后在主分支`master`上重演：

	$ git rebase master server

于是`server`的进度应用到`master`的基础上，而后快进主分支`master`：

	$ git checkout master
	$ git merge server

现在`client`和`server`分支的变化都被整合了，不妨删掉它们:

	$ git branch -d client
	$ git branch -d server

把提交历史变成如下图的样子：

															   (server)
																  |
																  |
	[c1]<--[c2]<--[c5]<--[c6]<--[c8']<--[c9']<--[c3']<--[c4']<--[c10'] 
										  |						  |
										  |						  |
									   (client)				   (master)

**衍合的风险**

一句话可以总结：

> 永远不要衍合那些已经推送到公共仓库的更新。  
> Do not rebase commits that you have pushed to a public repository.

(其实就是说一定要在分享之前衍合，分享出去之后就不要再衍合.)

在衍合的时候，实际上抛弃了一些现存的commit而创建了一些类似但不同的新commit。如果
你把commit推送到某处然后其他人下载并在其基础上工作，然后你用`git rebase`和`git push
 --force`重写了这些commit再推送一次，你的合作者就不得不重新合并他们的工作。

如果把衍合当作一种在推送之前清理提交历史的手段，而且仅仅衍合那些永远不会公开的commit，
那就不会有任何问题。

第4章：服务器上的Git
--------------------

Git远程仓库仅仅是一个纯仓库-- 一个没有当前工作目录的仓库。仓库里仅仅是Git的数据，
也即项目里`.git`目录里的内容，别无他物。

### 4.1 协议 ###

Git使用四种主要的协议来传输数据：本地传输，SSH协议，Git协议和HTTP协议。
除了HTTP协议以外，其它协议都要求在服务器端安装并运行Git。

**本地协议**

克隆一个本地仓库：

	$ git clone /path/to/project.git

添加一个本地仓库到现有Git工程：

	$ git remote add local_proj /path/to/project.git

然后就可以像在网络上一样向这个远程仓库推送和获取数据了。

**SSH协议**

克隆一个仓库：

	$ git clone ssh://git@github.com:kdlijian/kdlijian.github.com.git

或者不指明某个协议，这是Git会默认使用SSH：

	$ git clone git@github.com:kdlijian/kdlijian.github.com.git

**Git协议**

匿名访问，通常不能用来推送。传输最快。

**HTTP(s)协议**

	$ cd /var/www/htdocs
	$ git clone --bare /path/to/project/ git-project.git
	$ cd git-project.git
	$ cp hooks/post-update.sample hooks/post-update
	$ chmod a+x hooks/post-update

第5章：分布式Git
----------------

### 5.1 分布式工作流程 ###

**集中式工作流**


