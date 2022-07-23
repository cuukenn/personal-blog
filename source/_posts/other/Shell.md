---
title: shell脚本随记
tags:
  - shell
categories:
  - program
abbrlink: 5ab84bc0
date: 2022-07-22 12:00:00
---

##### 1.脚本相关
- ${a} 变量a的值, 在不引起歧义的情况下可以省略大括号。
- $(cmd) 命令替换，和`cmd`效果相同，结果为shell命令cmd的输，过某些Shell版本不支持$()形式的命令替换, 如tcsh。
- $((expression)) 和`exprexpression`效果相同, 计算数学表达式exp的数值, 其中exp只要符合C语言的运算规则即可, 甚至三目运算符和逻辑表达式都可以计算。

<!-- more -->

##### 2.脚本特殊变量
| 变量名 |含义|  
| :------------: | :------------: |
| $$ |Shell本身的PID（ProcessID）|  
| $! | Shell最后运行的后台Process的PID | 
| $? | 最后运行的命令的结束代码（返回值）|  
| $- | 使用Set命令设定的Flag一览 | 
| $* | 所有参数列表。如"$*"用「"」括起来的情况、以"$1 $2 … $n"的形式输出所有参数。|  
| $@ | 所有参数列表。如"$@"用「"」括起来的情况、以"$1" "$2" … "$n" 的形式输出所有参数。|  
| $# | 添加到Shell的参数个数 | 
| $0 | Shell本身的文件名 | 
| $1～$n | 添加到Shell的各参数值。$1是第1参数、$2是第2参数......|
##### 3.变量默认值
|表达式|含义|
| :------------: | :------------: |
| ${vari:-defaultValue}|当var没有定义时，此时使用defaultValue, 而vari依然为空，没有改变值|
|${vari:=defaultValue}|当vari没有定义时，此时使用defaultValue, 同时vari也被赋值为defaultValue|
| ${vari:?value}|当vari没有定义时，或者定义了值为空，将在终端报错并且退出，用于检查是否定义以及是否为空|
|${vari:+value}| 当vari定义并且不为空，将用value替换vari的值，否则什么也不做， 与${vari:-value}相反|
##### 4.四种模式匹配替换结构
|模式|含义|
| :------------: | :------------: |
|${variable%pattern}|在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最短的匹配模式|
|${variable%%pattern}|在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式|
|${variable#pattern}|在variable中查找，看它是否一给的模式pattern开始，如果是，就从命令行把variable中的内容去掉左边最短的匹配模式|
|${variable##pattern}|在variable中查找，看它是否一给的模式pattern结尾，如果是，就从命令行把variable中的内容去掉右边最长的匹配模式|
这四种模式中都不会改变variable的值，其中，只有在pattern中使用了*匹配符号时，%和%%，#和##才有区别。结构中的pattern支持通配符，*表示零个或多个任意字符，?表示仅与一个任意字符匹配，[...]表示匹配中括号里面的字符，[!...]表示不匹配中括号里面的字符。
##### 5.字符串替换与提取
##### 6.多条命令执行
- 单小括号，(cmd1;cmd2;cmd3) 新开一个子shell顺序执行命令cmd1,cmd2,cmd3, 各命令之间用分号隔开, 最后一个命令后可以没有分号。
- 单大括号，{ cmd1;cmd2;cmd3;} 在当前shell顺序执行命令cmd1,cmd2,cmd3, 各命令之间用分号隔开, 最后一个命令后必须有分号, 第一条命令和左括号之间必须用空格隔开。
对{}和()而言, 括号中的重定向符只影响该条命令， 而括号外的重定向符影响到括号中的所有命令。
---
更多: https://www.cnblogs.com/qlqwjy/p/8684630.html
