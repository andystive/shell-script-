# shell 编程
## 第一次整理
#### 时间：2018年12月28日
### 主要内容：
创建shell脚本文件时，必须在文件的第一行指定使用的shell
```
#!/bin/bash
```
```
#!/bin/bash
echo This is a test file
```
查看文件权限
```
ls -l 或 ll
```
alias 设置别名
```
vi .bashrc
alias ll='ls -lh'
source .bashrc
```
###### 文件权限
总共十位，分为4段，第一段表示文件类型，有-、p、d、l、s、c、b，-表示普通文件，d表示目录文件，l表示符号链接文件，c表示字符设备文件，b表示块设备文件，p表示命名管道文件，s表示socket文件。
>1. linux一切皆文件   
>2. 字符设备：键盘，/dev中可以看到     
>3. 块设备：硬盘
>4. 命名管道文件：FIFO（First in, First out）文件，mkfifo可以生成。       
>5. 管道只能用于父进程和子进程之间，或者拥有相同祖先的两个子进程之间，本质是内核中的一块缓存，在文件系统中以特殊的设备文件(管道文件)存在      
>6. socket文件：网络传输

从左到右，1-3代表文件所有者(owner)权限，4-6代表同组用户(group)权限，7-9代表其他用户(other)权限
读取权限为4，写入权限为2，执行权限为1
7（读取、写入、执行），6（读取、写入），5（读取、执行），4（读取），0（无权限）
```
- rw- r-- r--
- --- --- ---
pts/0
# mkfifo scriptfifo
pts/1
# cat scriptfifo
pts/0
script -f scriptfifo
```
添加执行权限
```
chmod u+x file
```
单引号:
>可以说是所见即所得：即将单引号内的内容原样输出，或者描述为单引号里面看到的是什么就会输出什么。

双引号:
>把双引号内的内容输出出来；如果内容中有命令、变量等，会先把变量、命令解析出结果，然后在输出最终内容来。


```
#!/bin/bash
#This script displays the date and who's logged on
echo -n The time and date are:
date
echo "Let's see who's logged into the system:"
who
```
$取值
```
#!/bin/bash
#display user information from the system.
echo "User info for userid: $USER"
echo UID: $UID
echo HOME: $HOME
```
#### **securecrt crtl+s 停止输入  ctrl+q 恢复输入**

## 第二次整理
#### 时间：2018年12月29日
### 主要内容：
用户变量
由字母、数字、下划线组成，长度不超过20，且区分大小写
= 赋值，自动判断变量值的数据类型

```
var1=10
var2=-57
var3=testing
var4="still more testing"
```

命令替换：从命令输出中提取信息，并将其赋给变量。
- [ ] 反引号字符（\`），键盘tab键上方
- [ ] $( ) 格式

```
testing=`date`
testing=$(date)
```

生成 181229.log 文件

```
#!/bin/bash
#copy the /usr/bin directory listing to a log file
today=$(date +%y%m%d)
ls /usr/bin -al > $today.log
```

###### > 输入重定向
###### >> 追加
###### < 输出重定向
###### << 内联重定向：无须使用文件进行重定向，只需在命令行中指定用于输入重定向的数据即可。

wc 对数据中的文本进行计数，默认情况下输出3个值；
- [ ] 文本的行数
- [ ] 文本的词数
- [ ] 文本的字节数

```
wc << EOF
test string 1
test string 2
test string 3
EOF
输出 3   9   42
```

EOF是end of file的缩写，通常在文本的最后存在此字符表示资料结束。脚本中前面的cat >...<< EOF表示读取下面的字符直到遇到EOF为止，脚本最后的EOF后面不能有空行，否则会报错。

```
#向index.php中写入phpinfo信息。
cat >apache/htdocs/index.php << EOF
<?php
phpinfo();
?>
EOF
```

管道（|），将前面命令的输出作为后面命令的输入    
常用的如查看调用80端口的服务

```
netstat -tulnp | grep 80
```

关系运算符：只支持数字不支持字符串
1. -eq：检测两个数是否相等，相等返回true
2. -ne：检测两个数是否不相等，不相等返回true
3. -gt：检测左边的数是否大于右边的，是则返回true
4. -lt：检测左边的数是否小于右边的，是则返回true
5. -ge：检测左边的数是否大于等于右边的，是则返回true
6. -le：检测左边的数是否小于等于右边的，是则返回true

```
#判断当前用户是否为root
if [ $UID -ne 0 ];then
    echo Not root user.
else
    echo Root user.
fi
```

## 第三次整理
#### 时间：2018年12月30日
### 主要内容：
各种括号的作用    
单小括号，常用与命令组和命令替换    
命令组：括号内的命令会新开一个shell顺序执行，括号内多个命令之间用分号分开

命令替换：$(cmd) 等同于 \`cmd\`   
在多层次的复合替换中，｀｀必须要额外的跳脱处理（反斜线），而$( )则不需要  
反引号(\`)需要用反斜线转义，$( )不需要转义
```
echo `echo '\\'`
输出 \
echo `echo '\\\'`
输出 \\
echo $(echo '\\')
输出 \\
```

双小括号，常用与算数运算比较。  
语法    
((表达式1,表达式2,...))
1. 双括号中可以直接使用a++,b--等表达式。
2. 双括号中可以进行for，while,if条件运算
3. 双括号中，所有变量可以不使用$符号。
4. 双括号中可以进行四则运算和逻辑运算。
5. 双括号中可以进行多个表达式运算，各个表达式之间用逗号(,S)分开
```
for((i=0;j<5;i++)) 等同于 for i in `seq 0 4`
a=1
echo((a=a+1))
```

单中括号
bash的内部命令，[ 和test是等同的，if/test结构中的左中括号是调用test的命令标识，右中括号是关闭条件判断的。
test/[ ]命令把它的参数作为比较表达式或者作为文件测试，并且根据比较的结果来返回一个退出状态码，结果为真则返回状态0
```
if [ "$(whoami)" == "root" ];then
	echo "当前用户为root，将进行部署"
注意：[ ] 两边和参数之间要有空格，否则报错。
```
[ ] 中可用的字符串比较运算符只有==和!=
[ ] 中可用的整数比较运算符有-eq,-gt等

双中括号    
[[是 bash 程序语言的关键字。，并不是一个命令。[[ ]] 结构比[ ]结构更加通用。在[[ ]]之间所有的字符都不会发生文件名扩展或者单词分割，但是会发生参数扩展和命令替换     
支持字符串的模式匹配，字符串比较时可以把右边的作为一个模式，而不仅仅是一个字符串，比如[[ hello == hell? ]]（注：问号为通配符，表示任1个字符），结果为真。[[ ]] 中匹配字符串或通配符，不需要引号。
&&、||、<和> 操作符能够正常存在于[[ ]]条件判断结构中，但是如果出现在[ ]结构中的话，会报错
```
if [[ $(whoami) == root ]];then
	echo "当前用户为root，将进行部署"
注意：[ ] 两边和参数之间要有空格，否则报错。
```

大括号
1. 对括号内的文件名做扩展，括号内不允许有空白，除非该空白被引用或转义。

```
touch {a,b}.txt
结果：生成a.txt和b.txt
touch {a..d}.txt
结果：生成a.txt、b.txt、c.txt、d.txt

文件名扩展
cp /etc/apt/sources.{list,list.bak}
cp /etc/apt/sources.list
cp /etc/apt/sources.list.bak

嵌套扩展
echo {a,b{1,2,3},c}
输出：a,b1,b2,b3,c

增量扩展
echo {1..10..2}
输出：1 3 5 7 9
```

2. 包含代码块，又被称为内部组，这个结构相当于创建了一个匿名函数,与小括号中的命令不同，大括号内的命令不会新开一个子shell运行，即脚本余下部分仍可使用括号内变量。
```
执行自动更新，将yes作为括号内部函数的参数输入
yes | {
    sudo apt-get update
}
一个比较危险的玩法
yes | {
    sudo rm -r /
    sudo dd if=/dev/zero of=/dev/sda
} 2>/dev/null
```
3. 变量替换
一般情况下，$var与${var}是没有区别的，但是用${ }会比较精确的界定变量名称的范围
```
A=B
echo ${A}B
输出：BB
```
常见通配符

字符 | 含义 | 实例
:--:| :--: | :--:
 * | 匹配0或多个字符 | a*b ab之间可以有任意长度的任意字符
 ？| 匹配任1个字符   | a?b ab之间只能有1个任意字符
 [list] | 匹配list中的任意单一字符 |  a[xyz]b ab之间只能有一个字符，且只能为x或y或z
 [!list] | 匹配除list中的任意单一字符 | a[!0-9]b ab之间只能有一个字符，且不能为阿拉伯数字
 [c1-c2] | 匹配c1-c2中任意单一字符 | a[0-9]b ab之间有且仅有一个阿拉伯数字
 {string1,string2,...} | 匹配string1或string2（或更多）其中一个字符串 | a{abc,123,xyz}b ab之间只能有个一字符串，且只能为abc或123或xyz

## 第四次整理
#### 时间：2018年12月31日
### 主要内容：
文件描述符
linux中一切皆文件，内核给每个访问的文件分配了文件描述符(File Descriptor)，它本质是一个非负整数，在打开或新建文件时返回，以后读写文件都要通过这个文件描述符。
文件描述符其实是一个索引值，指向每个进程打开文件的记录表，[POSIX](https://baike.baidu.com/item/POSIX)已经定义了STDIN_FILENO（标准输入文件）、STDOUT_FILENO（标准输出文件）和STDERR_FILENO（标准错误文件）三个常量，也就是0、1、2。这三个文件描述符是每个进程都有的，这也解释了为什么每个进程都有编号为0、1、2的文件而不会与其他进程冲突。

系统设定：
默认设备 | 标准文件 | 文件描述符 | 设备
:-------: | :--------: | :--------: | :--------:
默认输入设备 | 标准输入文件（STDIN_FILENO） | 0 | 键盘
默认输出设备 | 标准输出文件（STDOUT_FILENO） | 1 | 显示器
默认错误输出 | 标准错误文件（STDERR_FILENO）|  2 | 显示器

```
#列出/目录下的文件
ls /
输出：根目录下的文件和文件夹
ls / >/dev/null 2>&1 
输出：空
```
> /dev/null 文件可以理解为垃圾桶，信息到这里面就消失了，>/dev/null 相当于1 > /dev/null 即将标准输出上的信息重定向到垃圾桶，2>&1 即标准错误输出重定向（等同于）到标准输出，因为之前标准输出已经重定向到了空设备文件，所以标准错误输出也相当于重定向到空设备文件（垃圾桶）。

退出脚本
linux中的每个命令都是用退出状态码告诉shell它已经执行完毕。退出状态码是一个0-255的整数值。
linux中专门提供了变量（$?）来保存上个已执行命令的退出状态码，按照惯例，一个执行成功的命令的退出状态码为0，若一个命令退出时有错误发生，退出状态码就是一个正数值。
```
ls
输出：当前目录下的文件
echo $?
输出：0

asdfa
输出：command not found
echo $?
输出：127
```
linux退出状态码
状态码 | 描述
:----:|:----:
0 | 命令成功结束
1 | 一般性未知错误
2 | 不适合的shell命令
126 | 没有执行命令的正确权限
127 | 没找到命令
128 | 无效的退出参数
128+x | 与linux信号x相关的严重错误
130 | 通过ctrl+c退出的命令
255 | 正常范围之外的退出码

正常情况下，shell脚本会以脚本中最后一个命令的退出状态码退出，但可以改变这种默认行为，返回自己的状态码。
exit命令允许在脚本结束时指定一个退出状态码，退出状态码最大为255，若是超过255，则最终数值是指定退出码除以256后得到的余数。
```
#检测当前用户是否为root
if [[ $(whoami) == root ]];then
	echo "当前用户为root，将进行部署"
else
	echo "请更换root用户进行操作"
	exit 1
fi
```
exit允许使用变量作为退出状态码。
```
脚本内执行
#!/bin/bash
# testing the exit status
var1=10
var2=20
var3=$[var1 + var2]
exit $var3

echo $?
输出：30

```
##### 小结
1. bash shell脚本允许将多个命令串起来放进脚本中，创建脚本的最基本的方式是将命令行中的多个命令通过分号分开来，shell会按顺序逐个执行命令，在显示器上显示每个命令的输出。也可以创建一个shell脚本文件，将多个命令放进同一个文件，让shell依次执行。
2. shell脚本文件必须定义用于运行脚本的shell。这个可以通过#!符号在脚本文件的第一行指定，后面跟上shell的完整路径，常用的为#!/bin/bash。
3. 在shell脚本内，可以通过在变量前使用美元符来引用环境变量，也可以定义自己的变量以便在脚本内使用，并对其赋值，甚至还可以通过反引号或$()捕获的某个命令的输出。在脚本中可以通过在变量名前放置一个美元符来使用变量的值。
4. bash shell允许更改命令的标准输入和输出，将其重定向到其他地方。可以通过 > 将命令输出从显示器屏幕重定向到一个文件中。也可以通过 >> 将输出数据追加到已有文件。< 用来将输入重定向到命令。可以将文件内容重定向到某个命令。
5. Linux管道命令（|）允许将命令的输出直接重定向到另一个命令的输入。Linux系统能够同时运行这两条命令，将第一个命令的输出发送给第二个命令的输入，不需要借助任何重定向文件。
6. 在bash shell中，可以通过将美元符号放在由方括号包围的表达式之前来执行基本的运算。
7. shell中运行的每个命令都会产生一个退出状态码,退出状态码是一个0～255的整数值，表明命令是否成功执行；如果没有成功，可能的原因是什么。退出状态码0表明命令成功执行了。你可以在shell脚本中用exit命令来声明一个脚本完成时的退出状态码。



# **Markdown操作手册**
# 一级标题
## 二级标题
### 三级标题
#### 四级标题
##### 五级标题
###### 六级标题
[访问连接](https://www.baidu.com)

>Github 为软件开发者提供项目管理，代码托管

> 这是第一级引用
>
> > 这是第二级引用

> ## 这是第一个标题
> 1.这是第一行列表项   
> 2.这是第二行列表项   
>retuen shell_exec(`echo $input | $markdown_script`);

- Red
- Green
- Blue


1. Red
2. Green
3. Blue


* Reg
* Green
* Blue

* Github 有以下功能
  > 代码托管平台    
  > 在线运行环境

- [ ] 不勾选
- [x] 勾选

```
require 'redcarpet'
markdown = redcarpet.new("Hello World")
```

*斜体*   
**加粗**        
***加粗斜体***   
~~删除线~~   
***
---

First Header | Second Header | Third Header
:------------| :-----------: | -----------:
Left         | Center        | Right
Left         | Center        | Right

![结束语](http://pic1.win4000.com/mobile/2018-12-13/5c12068e80958.jpg "美女镇楼")
