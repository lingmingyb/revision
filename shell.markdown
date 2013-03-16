Bash Notes
==========

交互式的Bourne Shell
--------------------

###基础###

**退出状态**

命令或程序终止后，会向父进程返回一个退出状态。退出状态是一个0~255之间的整数。
按照惯例，程序退出时，如果返回的状态是0, 表示命令执行成功。如果退出状态非0,
则表示命令因某种原因而导致失败。如果shell没有找到命令，返回的状态是127。如果
是一个致命的信号导致程序终止，则退出状态是128加上导致它终止的信号编号。

	$ grep root /etc/pasawd
	$ echo $?
	0

	$ grep collie /etc/passwd
	$ echo $?
	1

	$ grep root null_file
	$ echo $?
	2

	$ grrrep root /etc/passwd
	$ echo $?
	127

  	$ grep root /etc/passwd ^C
	$ echo $?
	130							# 128+2


**含多条命令的命令行**

* 	命令顺序执行:

		$ ls; pwd; date

	从左到又逐一执行，直到遇到换行符。

* 	命令编组:

		$ (ls; pwd; date) > outputfile

	把多条命令合为一组，这样能将所有命令的输出通过管道发送给另一条命令，或者
	重定向到某个文件。

* 	命令条件执行:

		$ cc prgm1.c -o prgm1 && prgm1

	如果第一条命令成功，就执行第二条命令。

		$ cc prgm1.c >& err || mail bob err

	如果第一条命令失败，就执行第二条命令。

*	命令后台执行：

		$ man bash | lp&
		$ echo $!

	
###变量###

**变量类型**

shell变量可分两类：局部变量和环境变量。

局部变量只在创建它们的shell中可用，而环境变量(全局变量)则可在创建它们的shell及其派生
出来的任意子进程中使用。

设置局部变量：

	$ name="mellon"

或者

	$ declare name="mellon"
	
设置环境变量:
	
	$ export NAME="mellon"

或者

	$ declare -x NAME="mellon"

也可

	$ name="mellon"; export name

**复位变量**

只要不被设为只读，局部变量和环境变量都可以用`unset`命令复位。

	$ unset name
	$ unset NAME

**输出变量**

`echo`加上`-e`选项，允许使用大量控制输出外观的转义序列。

	$ echo $name
	$ echo -e "\t\tHello there"

**变量扩展修饰符**

	${variable:-word}
	${variable:=word}
	${variable:+word}
	${variable:?word}
	${variable:offset}
	${variable:offset:length}

**字串的变量扩展**

	${variable%pattern}
	${variable%%pattern}
	${variable#pattern}
	${variable##pattern}
	${#varible}

**位置参量**

	$ set a b c d e f g h i j k m n
	$ echo $0
	$ echo $1
	$ echo ${10}
	$ echo $#
	$ echo $*
	$ echo "$@"

	$ set --

**其他特殊变量**

	$ echo $?
	$ echo $!
	$ echo $-
	$ echo $$

###引用###

单引号和双引号必须成对出现。

双引号可以保护单引号，单引号可以保护双引号。

单引号可以保护所有特殊字符免受解释(除了它本身);

双引号允许处理变量替换(`$`)和命令替换(`` `command` ``), 这时可以加``\``
转义这两种字符以及它本身。

###命令替换###


所有shell都使用反引号执行命令替换，bash有自己的方式，使用反引号是为了向上兼容。

执行扩展时，bash先执行命令，然后返回命令的标准输出，输出结果末尾的换行符都将被
删除。

当使用旧风格的反引号格式进行替换时，反斜杠将保留它的字面含义，除非它后面跟的是
`$`, `` ` ``或`\`。

当使用`$(command)`格式时，括号内的所有字符只用于构成命令，都不会被特殊对待。

**嵌套**

	$ echo `basename \`pwd\``
	$ echo $(basename $(pwd))

**输出**

默认剥掉换行符：

	$ echo $(cal)

保留换行符：

	$ echo "$(cal)"

###算数扩展###

	$((expression))

	$[ expression ]

###数组###

	$ declare -a array
	$ array=(a b c)

	$ echo ${array[0]}
	$ echo ${array[*]}
	$ echo ${#array[*]}

	$ unset array


Bash shell编程
--------------

一个bash shell程序由一组UNIX/Linux命令、bash shell命令、程序结构控制语句和注释
组成。


