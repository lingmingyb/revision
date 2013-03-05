GNU grep Notes
==============

The Basic
---------

>grep = g/RE/p

>In GNU grep, there is no difference in available functionality 
>between basic and extended syntaxes; in other impletions, basic 
>regular expressions are less powerful.

>In basic regular expressions the meta-characters `? + | { ( and )`
>lose their special meaning; instead use the backslashed verson:
>`\? \+ \| \{ \( \)`. 

via `man 1 grep`

Some Tests
----------

    $ grep -p '[[.ch.]]' Regex.txt
grep:POSIX collating elements are not supported.

	$ grep -E "3{2}" Regex.txt
syntax ok.

	$ grep "3{2}" Regex.txt		
syntax error.

	$ grep "3\{2\}" Regex.txt	
syntax ok.

	$ grep "[a-z]" Regex.txt (-o)
每个match都为单个字母。
	
	$ grep "[a-z]*" Regex.txt (-o)
每个match多为连续的字母序列。
这项差别如果不用-o选项竟然看不出来。

-EOF- 2013/2/28
