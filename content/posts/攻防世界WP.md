---
title: "攻防世界WP"
date: 2021-07-07T18:14:33+08:00
draft: false
---

# 攻防世界WP

## 新手区

### 	题目 get  shell

#### 	题目描述：

运行就能拿到shell呢，真的

#### 	题目分析：

nc链接，运行，拿到shell

#### 	解题：

1.nc链接IP（端口是空格）

![image-20201230121218856](/images/image-20201230121218856.png)

2.ls查看文件，cat flag  获得flag

![image-20201230121305114](/images/image-20201230121305114.png)



### 题目 CGfsb

#### 	题目描述：

菜鸡面对着pringf发愁，他不知道prinf除了输出还有什么作用

#### 	题目分析：

1.从题目描述来看，解题的关键是prinf

2.查看文件格式，保护设置，运行一下

![image-20201230150149289](/images/image-20201230150149289.png)

![image-20201230150235986](/images/image-20201230150235986.png)

![image-20201230150336918](/images/image-20201230150336918.png)

发现是一个32位的文件，开启了NX、Stack保护

![image-20201230150606994](/images/image-20201230150606994.png)

这是程序的流程

3.结合分析，应该和格式化字符串漏洞有关，放到IDA中具体看一下

![image-20201230151140410](/images/image-20201230151140410.png)

这是一个高危漏洞！！！具体用法先放着，往下看。

![image-20201230150911401](/images/image-20201230150911401.png)

看到这边有一个system函数，里面的内容是“cat flag”，这就是答案！！！

而想要运行这行代码需要一个条件 pwnme==8，这时候想到前面有一个漏洞。

4.格式化字符串漏洞的简单运用

%n：将%n之前printf已经打印的字符个数赋值给偏移处指针所指向的地址位置，如%100×10$n表		示将0x64写入偏移10处保存的指针所指向的地址

#### 解题：

1.已经知道了存在格式化字符串漏洞，开始利用，查看偏移量

![image-20201230152812609](/images/image-20201230152812609.png)

输入aaaa，发现0x61616161（aaaa）偏移量为10

2.编写脚本

![image-20201230153908170](/images/image-20201230153908170.png)

payload解释：将pwnme的地址转换为一个4字节的数，因为前面输出8个字符，通过%10$n，可以将8写入第10个偏移量对应的地址（pwenme的地址）里

3.运行

![image-20201230154418227](/images/image-20201230154418227.png)

拿到falg

4.注：

还可以这样写payload=‘aaaa’+p32（0x0804A068）+'%11$n'

### 题目 when_did_you_born

#### 	题目描述：

只要知道你的年龄就能获得flag，但菜鸡发现无论如何输入都不正确，怎么办

#### 	题目分析：

1.从描述来看，关键在于“年龄”

2.查看文件格式，保护设置，运行一下

![image-20210106192203024](/images/image-20210106192203024.png)

64位，开启了stack、NX保护。

![image-20210106192325605](/images/image-20210106192325605.png)

这是程序基本流程。

3.扔到IDA中看一下

![image-20210106192634693](/images/image-20210106192634693.png)

答案就在这！！！

整体看一下

![image-20210106192907254](/images/image-20210106192907254.png)

所以思路很明显了，想办法去执行system就可以了。但问题是两个if不能一起执行。此时注意到前面有一个

![image-20210106193436330](/images/image-20210106193436330.png)

漏洞……

查看一下

![image-20210106195023052](/images/image-20210106195023052.png)

v4下面正好是v5，那就可以直接覆盖了

#### 解题：

![image-20210106195107572](/images/image-20210106195107572.png)

![image-20210106195145601](/images/image-20210106195145601.png)

解决。

### 题目 hello_pwn

#### 题目描述：

pwn！，segment fault! 菜鸡陷入了深思

#### 题目分析：

1.题目描述貌似没啥用，不管了

2.查看文件，保护，运行一下

![image-20210107164704510](/images/image-20210107164704510.png)

64位，开启了NX保护

![image-20210107164749946](/images/image-20210107164749946.png)

就一句话……

3.扔到IDA中看一下

![image-20210107165025779](/images/image-20210107165025779.png)

可以看到只要运行这个函数就可以拿到flag

![image-20210107165102382](/images/image-20210107165102382.png)

这是条件。

这时候发现一个漏洞

![image-20210107165136562](/images/image-20210107165136562.png)

![image-20210107165150398](/images/image-20210107165150398.png)

好了，可以直接利用溢出完成题目了

#### 解题：

![image-20210107165552530](/images/image-20210107165552530.png)

![image-20210107165618691](/images/image-20210107165618691.png)

完成。

### 题目 level0

#### 题目描述：

菜鸡了解了什么是溢出，他相信自己能得到shell

#### 题目分析：

1.从分析来看，解题关键是拿到shell

2.查看文件，保护，运行

![image-20210107212219583](/images/image-20210107212219583.png)

64位，NX保护。

![image-20210107212327276](/images/image-20210107212327276.png)

嗯，就一句话，没啥用。

3.扔进IDA里面看一下

![image-20210107212507099](/images/image-20210107212507099.png)

发现这个函数，那么只要让它执行这个函数，就可以拿到shell了。

![image-20210107212619490](/images/image-20210107212619490.png)

发现这边有一个溢出。开始编写脚本。

#### 解题：

编写脚本

#### ![image-20210107213418120](/images/image-20210107213418120.png)

利用一个溢出将callsystem函数的地址编写进去

![image-20210107213611957](/images/image-20210107213611957.png)

完成。

### 题目 level2

#### 题目描述：

菜鸡请教大神如何获得flag，大神告诉他‘使用’面向返回的编程（ROP）就可以了

#### 题目分析：

1.从题目描述可知，要用到ROP技术。

2.查看文件格式，保护，运行。

![image-20210107215132075](/images/image-20210107215132075.png)

32位，NX保护

![image-20210107215211149](/images/image-20210107215211149.png)

程序流程。

3.扔进IDA里看一下。

![image-20210107215433049](/images/image-20210107215433049.png)

存在system函数，可以将‘/bin/sh’，写入，获得shell

![image-20210107215534512](/images/image-20210107215534512.png)

存在溢出漏洞。可以开始编写脚本了

#### 解题：

![image-20210107223902494](/images/image-20210107223902494.png)

构建system（/bin/sh），获得shell

![image-20210107223948673](/images/image-20210107223948673.png)

完成。

### 题目 string

#### 题目描述：

菜鸡遇到了Dragon,有一位巫师可以帮助他逃离危险，但似乎需要一些要求

#### 题目分析：

1.从题目描述来看，解题的关键在“巫师”

2.查看文件格式，保护。

![image-20210108094737987](/images/image-20210108094737987.png)

64位，保护除了PIE都开了。

3.扔到IDA里面看一下。（400CA6）

![image-20210108095248994](/images/image-20210108095248994.png)

ok，找到巫师了，不过，需要条件*a1==a1[1]。

![image-20210108095535279](/images/image-20210108095535279.png)

观察一下，可以发现*a1==a1[1],即要满足 *v3==v3[1]。

![image-20210108095741998](/images/image-20210108095741998.png)

这时候发现这边存在一个输出漏洞。（printf(&……，&……)）

想到使用%n来修改值。

查看一下偏移量。

![image-20210108100559729](/images/image-20210108100559729.png)

偏移量是7。

再来找一下地址。

![image-20210109191620499](/images/image-20210109191620499.png)

这边泄露了v4地址，即v3

![image-20210108104853416](/images/image-20210108104853416.png)

并且出现了可以将v1变成一个函数来执行外部命令的语句。

#### 解题：

![image-20210109191719627](/images/image-20210109191719627.png)

![image-20210109191845878](/images/image-20210109191845878.png)

![image-20210109191915466](/images/image-20210109191915466.png)

完成。

### 题目 int_overflow

#### 题目描述：

菜鸡感觉这题似乎没有办法溢出，真的么？

#### 题目分析：

1.从题目来看，这题解题关键在于“int_overflow”

2.查看文件，保护，运行

![image-20210111200827431](/images/image-20210111200827431.png)

![image-20210111201002073](/images/image-20210111201002073.png)

3.扔进IDA里面看一下

![image-20210111201440496](/images/image-20210111201440496.png)

一个溢出漏洞

![image-20210111201508483](/images/image-20210111201508483.png)

条件

这时候联想到题目的提示int_overflow

![image-20210111201549472](/images/image-20210111201549472.png)

无符号int型，最大256，257开始长度变为1

![image-20210111202408010](/images/image-20210111202408010.png)

又发现这个……

#### 解题：

![image-20210111202818786](/images/image-20210111202818786.png)

![image-20210111202909100](/images/image-20210111202909100.png)

![image-20210111202841356](/images/image-20210111202841356.png)

完成。



### 题目 cgpwn2

#### 题目描述：

菜鸡认为自己需要一个字符串

#### 题目分析：

1.查看文件，保护，运行。

![image-20210111203448731](/images/image-20210111203448731.png)

![image-20210111203523675](/images/image-20210111203523675.png)

2.扔进IDA里面看一下

![image-20210111203716307](/images/image-20210111203716307.png)

很明显的一个漏洞

![image-20210111203739487](/images/image-20210111203739487.png)

调用了system函数，但是打印“hehehe”。去找了一下有没有“/bin/sh”，没找到。只能自己输入了

#### 解题：

![image-20210111205429073](/images/image-20210111205429073.png)

![image-20210111205457550](/images/image-20210111205457550.png)

![image-20210111205518341](/images/image-20210111205518341.png)

完成。