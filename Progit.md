Progit Notes
============

第1章：起步
------------

### 设计哲学 ###

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

  

### 三种状态 ###

对于任何一个文件，在Git内部都只有三种状态，已修改(modified), 已暂存(staged)
和已提交(commited)。

从项目中取出某个版本的所有文件和目录，用以开始后续工作的叫做工作目录。
所谓暂存区不过是个简单的文件，含有下一次提交将要发生的内容的信息。

基本的Git工作流程如下所示：

1. 修改：在工作目录中修改某些文件;
2. 暂存：对这些修改了的文件做快照,并将其保存到暂存区域;
3. 提交：提交更新，将保存在暂存区的文件快照永久储存到Git目录中。

### 配置Git ###

	$ git config --system user.name		`/etc/gitconfig`
	$ git config --global user.name		`~/.gitconfig`
	$ git config ???	  user.name		`.git/config`

### 获取帮助 ###

	$ git help <command>
	$ git <command> --help
	$ man git-<command>


第2章：Git基础
--------------

### File Status Lifecycle ###

	(Untracked)		(Unmodified)	(Modifiled)      (Staged)
   		|				|				|				|
   		| add the file	| edit the file	|stage the file |
   		|-------------->|-------------->|-------------->|
   		|				|				|				|
   		|remove the file|    		 Commit				|
   		|<-------------	|<------------------------------|
   		|				|				|				|	


### 创建新仓库：###

	$ cd ~/Notes
	$ git init

或者

	$ git clone git://url/to/.git (mygitrepo)

现在我们已经有了一个真实项目的Git仓库，并且从仓库中取出了最新版本
的文件拷贝。

工作目录下的所有文件不外乎这两种状态：已跟踪或未跟踪。初次克隆某个
仓库时，工作目录所有的文件都属于已跟踪文件，而且状态属于未更改。而
在一个已存在目录内初始化Git仓库后，工作目录所有文件都属于未跟踪。

### 检查当前文件状态：###
	
	$ git status

### 跟踪新文件：###

	$ mv ~/Progit.md Noets/
	$ git add Progit.md

### 暂存已修改文件：###

	$ git add Progit.md

(`git add`是个多功能命令，根据目标文件状态的不同，此命令的效果也不同：
可以用它开始跟踪新文件，或者把已跟踪并修改的文件放入暂存区，还能用于
合并时把有冲突的文件标记为已解决状态等。)

### 忽略某些文件：###

	$ cat .gitignore

	# 该行为注释
	*.swp

### 查看已暂存和未暂存的更新：###

	$ git diff
查看哪些更新还没有暂存。

	$ git diff --staged

或者
	
	$ git diff --cached

查看哪些更新已经暂存起来等待下次提交。

### 提交更新：###

	$ git commit -m "the very first commit"

### 跳过使用“暂存区域”：###

尽管使用暂存区域可以精心地准备要提交的细节，可有时候略显繁琐。
在提交的时候，给`git commit`加上`-a`选项，Git就会把所有已跟踪并
更改了的文件暂存起来一并提交，从而跳过`git add`步骤。

	$ git commit -a 

### 移除文件 ###

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

### 移动文件 ###

给被Git跟踪的某个文件重命名：
	
	$ git mv README.txt README
	$ git commit

实际上相当于运行了下面三个命令：
	
	$ mv README.txt README
	$ git rm README.txt
	$ git add README
	$ git commit

### 查看提交历史 ###

	$ git log [arguments]

	$ git log -<n>
	$ git log --pretty=[arg]
	$ git log --pretty=format
	$ git log -p
	$ git log --author --commiter --all-match
	$ git log --since="date" --before="date"  --<file>

### 撤销操作 ###

### 修改最近一次提交 ###

	$ git commit --amend

此命令将使用当前的暂存快照提交，并且有机会修改提交说明。

如果上次提交时忘了暂存某些修改，可以先不上暂存操作，然后再`--amend`提交：

	$ git commmit -m 'yet another commit'
	$ git add forgotten_file
	$ git commit --amend

### 取消已经暂存的文件 ###

	$ git reset HEAD <file>

### 取消对文件的修改 ###

	$ git checkout -- <file> 

这条命令有些危险，所有对文件test1刚才的修改都没了。如果只是想要回退版本，同时
保留刚才的修改以便将来继续工作，可以使用下章介绍的stashing和分支来处理。

记住，任何提交到Git的数据，都可以被恢复。可能失去的数据仅限于没有提交果的，
对Git来说它们就像从未存在过一样。

### 远程仓库的使用 ###

查看当前的远程库：
		
	$ git remote [-v]

在克隆完某个项目后，至少可以看到一个名为origin的远程库，Git默认使用这个名字
来标识你所克隆的原始仓库。

添加远程仓库：
	
	$ git remote add [remote-name] [url]

从远程仓库抓取数据：

	$ git fetch [remote-name]

该命令回到远程仓库中拉取所有你本地仓库中还没有的数据。运行完成后，你就可以在
本地访问该远程仓库中的所有分支，将其中某个分支合并到本地，或者只是取出某个分支
一探究竟。

`fetch`命令只是将远端的数据拉到本地仓库，并不自动合并到当前分支，当准备好的时候
可以手动合并。而`pull`命令则将远端分支自动合并到工作目录中当前分支。

推送数据到远程仓库：

	$ git push [remote-name] [branch-name]

如果要把本地的`master`分支推送到`origin`服务器上(`git clone`操作会自动使用
`master`和`origin`名字), 可以运行下面这个命令：

	$ git push origin master

只有在克隆的服务器上有写权限，或者同一时刻没有人在推数据，这条命令才可如期
完成任务。如果你在推数据前，已有人推送了若干更新，那你的推送操作就会被驳回。
你必须先把它们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送。

查看远程仓库信息：

我们可以通过命令
	
	$ git remote show [remote-name]

来查看某个仓库的远程信息。比如要查看所克隆的远程仓库，可以运行：

	$ git remote show origin

远程仓库的删除和重命名：

	$ git remote rename pb paul

	$ git remote rm paul

### 打标签 ###


