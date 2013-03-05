正则表达式笔记
=============

### 综述 ###

正则表达式是用来匹配某些字符串的模式表达式。

目前主要有两种正则表达式：

*	Modern(Extended) Regular Expressions (ERE);
*	Obsolete(Basic) Regular Expressions (BRE);

### 元字符 ###

有两种常见的元字符，shell元字符和正则表达式元字符。shell元字符由shell解释，
正则表达式元字符由各执行模式匹配操作的程序解释。两种元字符的意义不一样。

### 基本元字符(BRE) ###

所有UNIX模式匹配工具都适用。

+ `^`      	Matching the null string at the beginning of a line;
+ `&` 		Matching the null string at the end of a line;
+ `.` 		Matching any single character;
+ `*` 		Mathching a sequence of 0 or more matches of the preceding item;
+ `[ ]` 	Matching any single character from the list;
+ `[^ ]` 	Matching any single character not from the rest of the list;
+ `[x-y]` 	Matching any single character in the full range of characters  
			between those two(Mind the LC_COLLATE please);
+ `\` 		Followed by one of these characters:`^ . [ $ ( ) | * - ? \`,  
			matching that character taken as an ordinary character.

### 一些扩展元字符 ###

有限UNIX程序支持。

+ `\<`		Matching the null string at the beginning of a word;
+ `\>` 		Matching the null string at the end of a word;  
			(word-constituent characters are letters, digits and _s)
+ `x\{m\}`	
+ `x\{m,\}` They are Bounds.
+ `x\{m,n\}`
+ `\()\`	back references, avoid using them( via `man 7 regex`)

### 更多元字符 ###

#### Repetition ####

+ `+` 		The preceding item wil be matched one or more times;
+ `?` 		The preceding item is optional and matched at most once;

#### Named Classes ####

	[:alnum:]  
	[:alpha:]
	.
	.
	.
	[:xdigit:]

### Bracket Expressions ###

Most meta-characters lose their special meaning inside bracket expressions.

In a bracket expression,

+ To include a literal `]`, place it first in the list.
+ To include a literal `^`, place it anywhere but first.
+ To include a literal `-`, place it last.

### Collating elements ###

+ `[.xy.]`
+ `[[.-.]-z]` To use `-` as the first endpoint of a range;
+ `[[=x=]]`	  Equivalence class.

### Miscellaneous ###

In the event that an RE could match more than on substring of a given 
string, the RE matches the one starting earliest in the string; if the RE 
could match more that one substring starting at the same point, it matches 
the longest.

-EOF-         2013/2/28
