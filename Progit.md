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
