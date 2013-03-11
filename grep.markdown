GNU grep Notes
==============

grep = g/RE/p

BRE vs. ERE
-----------

In GNU grep, there is no difference in available functionality 
between basic and extended syntaxes; in other impletions, basic 
regular expressions are less powerful.

In basic regular expressions the meta-characters `? + | { (` and `)`
lose their special meaning; instead use the backslashed verson:
`\? \+ \| \{ \( \)`. 

via `man 1 grep`

Some Tests
----------

    $ grep -p '[[.ch.]]' Regex.txt
	grep:POSIX collating elements are not supported.

	$ grep -E "3{2}" Regex.txt		# OK
	$ grep "3{2}" Regex.txt			# Error	
	$ grep "3\{2\}" Regex.txt		# OK

	$ grep "[a-z]" Regex.txt (-o)	#每个match都为单个字母。
	$ grep "[a-z]*" Regex.txt (-o)	#每个match多为连续的字母序列。

	$ grep '[^0-9]' datafile		#不是打印“不含数字的行”，而是打印
									#所有“包含一个非数字存在的行”。

	$ grep 'ss* ' datafile			#代表s后跟0或多个连续的s, 再跟一空格;
									#`s*`是一个整体，不是`s+*`

	$ grep '\<[a-z].*n\>' datafile		# \< \>成对出现并不限定一个单词
	northwest	NW	Charles Main	

	$ grep -w '\[a-z].*n\n' datafile	#结果同上(The match forms whole words!)
										#As defined in `man 1 grep`
	
	$ grep -i "$LOGNAME" datafile		#变量会被Shell展开;
	$ grep -i '$LOGNAME' datafile		#变量不会被Shell展开

	$ grep '^n\w*\W' datafile			#GNU grep支持\w, \W
	$ grep '\bnorth\b'	datafile		#和\b

