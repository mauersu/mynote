#项目中出现的乱码问题的解决与字符编码分析
##1.项目中出现的乱码问题的解决
###出现情景
&emsp;&emsp;项目service层与controller层由dubbo进行耦合，其中一套部署在linux服务器上，由于本地分支测试需要，我将windows本机service层注册到了同一zookeeper上，进行自己模块测试，导致项目组其他成员测试机测试时，部分请求落到了我的机器上，其中登录模块有存储用户信息到redis，逻辑在service层，其中存储时请求走的是windows主机，使用时使用的是linux主机
###问题分析
&emsp;&emsp;由于windows默认编码是gbk，而linux默认编码是utf-8，所以导致存入redis的为windows的gbk编码的byte数组，而取出时又用linux的utf-8解码，导致出现乱码。
###问题解决
&emsp;&emsp;终究其原因，是因为在字符转换为byte数组时，未指明编码格式使用系统默认编码所致，将转化时都指明为utf-8，问题解决
##2.字符编码分析
###Unicode（统一码、万国码、单一码）
&emsp;&emsp;Unicode是国际组织制定的可以容纳世界上所有文字和符号的字符编码方案。Unicode用数字0-0x10FFFF来映射这些字符，最多可以容纳1114112个字符，或者说有1114112个码位。码位就是可以分配给字符的数字。UTF-8、UTF-16、UTF-32都是将数字转换到程序数据的编码方案。

&emsp;&emsp;Unicode定义的字符集已经超过16位所能表达的范围，把所有这些CodePoint分成17个代码平面（Code Plane）：

* U+0000 ~ U+FFFF划入基本多语言平面（Basic MultilingualPlane，简记为BMP）；
* 其余划入16个辅助平面（Supplementary Plane），代码点范围U+10000 ~ U+10FFFF。
###
