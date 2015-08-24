#项目中出现的乱码问题的解决与字符编码分析

##1.项目中出现的乱码问题的解决
###出现情景
&emsp;&emsp;项目service层与controller层由dubbo进行耦合，其中一套部署在linux服务器上，由于本地分支测试需要，我将windows本机service层注册到了同一zookeeper上，进行自己模块测试，导致项目组其他成员测试机测试时，部分请求落到了我的机器上，其中登录模块有存储用户信息到redis，逻辑在service层，其中存储时请求走的是windows主机，使用时使用的是linux主机
###问题分析
&emsp;&emsp;由于windows默认编码是gbk，而linux默认编码是utf-8，所以导致存入redis的为windows的gbk编码的byte数组，而取出时又用linux的utf-8解码，导致出现乱码。
###问题解决
&emsp;&emsp;终究其原因，是因为在字符转换为byte数组时，未指明编码格式使用系统默认编码所致，将转化时都指明为utf-8，问题解决
##2.字符编码分析

> Unicode、Unicode big endian和UTF-8编码的txt文件的开头会多出几个字节，分别是FF、FE（Unicode）,FE、F  （Unicode big endian）,EF、BB、BF（UTF-8）

###big endian和little endian

&emsp;&emsp;big endian和little endian是CPU处理多字节数的不同方式。例如“汉”字的Unicode编码是6C49。那么写到文件里时，究竟是将6C写在前面，还是将49写在前面？如果将6C写在前面，就是big endian。还是将49写在前面，就是little endian。

&emsp;&emsp;“endian”这个词出自《格列佛游记》。小人国的内战就源于吃鸡蛋时是究竟从大头(Big-Endian)敲开还是从小头(Little-Endian)敲开，由此曾发生过六次叛乱，其中一个皇帝送了命，另一个丢了王位。

&emsp;&emsp;我们一般将endian翻译成“字节序”，将big endian和little endian称作“大尾”和“小尾”。

###字符编码、内码，顺带介绍汉字编码

&emsp;&emsp;字符必须编码后才能被计算机处理。计算机使用的缺省编码方式就是计算机的内码。早期的计算机使用7位的ASCII编码，为了处理汉字，程序员设计了用于简体中文的GB2312和用于繁体中文的big5。

&emsp;&emsp;GB2312(1980年)一共收录了7445个字符，包括6763个汉字和682个其它符号。汉字区的内码范围高字节从B0-F7，低字节从A1-FE，占用的码位是72*94=6768。其中有5个空位是D7FA-D7FE。

&emsp;&emsp;GB2312支持的汉字太少。1995年的汉字扩展规范GBK1.0收录了21886个符号，它分为汉字区和图形符号区。汉字区包括21003个字符。2000年的GB18030是取代GBK1.0的正式国家标准。该标准收录了27484个汉字，同时还收录了藏文、蒙文、维吾尔文等主要的少数民族文字。现在的PC平台必须支持GB18030，对嵌入式产品暂不作要求。所以手机、MP3一般只支持GB2312。

&emsp;&emsp;从ASCII、GB2312、GBK到GB18030，这些编码方法是向下兼容的，即同一个字符在这些方案中总是有相同的编码，后面的标准支持更多的字符。在这些编码中，英文和中文可以统一地处理。区分中文编码的方法是高字节的最高位不为0。按照程序员的称呼，GB2312、GBK到GB18030都属于双字节字符集 (DBCS)。

&emsp;&emsp;有的中文Windows的缺省内码还是GBK，可以通过GB18030升级包升级到GB18030。不过GB18030相对GBK增加的字符，普通人是很难用到的，通常我们还是用GBK指代中文Windows内码。

&emsp;&emsp;这里还有一些细节：

&emsp;&emsp;GB2312的原文还是区位码，从区位码到内码，需要在高字节和低字节上分别加上A0。

&emsp;&emsp;在DBCS中，GB内码的存储格式始终是big endian，即高位在前。

&emsp;&emsp;GB2312的两个字节的最高位都是1。但符合这个条件的码位只有128*128=16384个。所以GBK和GB18030的低字节最高位都可能不是1。不过这不影响DBCS字符流的解析：在读取DBCS字符流时，只要遇到高位为1的字节，就可以将下两个字节作为一个双字节编码，而不用管低字节的高位是什么。


###Unicode、UCS和UTF

&emsp;&emsp;前面提到从ASCII、GB2312、GBK到GB18030的编码方法是向下兼容的。而Unicode只与ASCII兼容（更准确地说，是与ISO-8859-1兼容），与GB码不兼容。例如“汉”字的Unicode编码是6C49，而GB码是BABA。

&emsp;&emsp;Unicode也是一种字符编码方法，不过它是由国际组织设计，可以容纳全世界所有语言文字的编码方案。Unicode的学名是"Universal Multiple-Octet Coded Character Set"，简称为UCS。UCS可以看作是"Unicode Character Set"的缩写。

&emsp;&emsp;根据维基百科全书的记载：历史上存在两个试图独立设计Unicode的组织，即国际标准化组织（ISO）和一个软件制造商的协会（unicode.org）。ISO开发了ISO 10646项目，Unicode协会开发了Unicode项目。

&emsp;&emsp;在1991年前后，双方都认识到世界不需要两个不兼容的字符集。于是它们开始合并双方的工作成果，并为创立一个单一编码表而协同工作。从Unicode2.0开始，Unicode项目采用了与ISO 10646-1相同的字库和字码。

&emsp;&emsp;目前两个项目仍都存在，并独立地公布各自的标准。Unicode协会现在的最新版本是2005年的Unicode 4.1.0。ISO的最新标准是10646-3:2003。

&emsp;&emsp;UCS规定了怎么用多个字节表示各种文字。怎样传输这些编码，是由UTF(UCS Transformation Format)规范规定的，常见的UTF规范包括UTF-8、UTF-7、UTF-16。

&emsp;&emsp;IETF的RFC2781和RFC3629以RFC的一贯风格，清晰、明快又不失严谨地描述了UTF-16和UTF-8的编码方法。我总是记不得IETF是Internet Engineering Task Force的缩写。但IETF负责维护的RFC是Internet上一切规范的基础。


###UCS-2、UCS-4、BMP

&emsp;&emsp;UCS有两种格式：UCS-2和UCS-4。顾名思义，UCS-2就是用两个字节编码，UCS-4就是用4个字节（实际上只用了31位，最高位必须为0）编码。下面让我们做一些简单的数学游戏：

&emsp;&emsp;UCS-2有2^16=65536个码位，UCS-4有2^31=2147483648个码位。

&emsp;&emsp;UCS-4根据最高位为0的最高字节分成2^7=128个group。每个group再根据次高字节分为256个plane。每个plane根据第3个字节分为256行 (rows)，每行包含256个cells。当然同一行的cells只是最后一个字节不同，其余都相同。

&emsp;&emsp;group 0的plane 0被称作Basic Multilingual Plane, 即BMP。或者说UCS-4中，高两个字节为0的码位被称作BMP。

&emsp;&emsp;将UCS-4的BMP去掉前面的两个零字节就得到了UCS-2。在UCS-2的两个字节前加上两个零字节，就得到了UCS-4的BMP。而目前的UCS-4规范中还没有任何字符被分配在BMP之外。


###UTF编码

&emsp;&emsp;UTF-8就是以8位为单元对UCS进行编码。从UCS-2到UTF-8的编码方式如下：

 * UCS-2编码(16进制)	UTF-8 字节流(二进制)
* 0000 - 007F&emsp;&emsp;&emsp;&emsp;0xxxxxxx
* 0080 - 07FF&emsp;&emsp;&emsp;&emsp;110xxxxx 10xxxxxx
* 0800 - FFFF&emsp;&emsp;&emsp;&emsp;1110xxxx 10xxxxxx 10xxxxxx

&emsp;&emsp;例如“汉”字的Unicode编码是6C49。6C49在0800-FFFF之间，所以肯定要用3字节模板了：1110xxxx 10xxxxxx 10xxxxxx。将6C49写成二进制是：0110 110001 001001， 用这个比特流依次代替模板中的x，得到：11100110 10110001 10001001，即E6 B1 89。

&emsp;&emsp;读者可以用记事本测试一下我们的编码是否正确。

&emsp;&emsp;UTF-16以16位为单元对UCS进行编码。对于小于0x10000的UCS码，UTF-16编码就等于UCS码对应的16位无符号整数。对于不小于0x10000的UCS码，定义了一个算法。不过由于实际使用的UCS2，或者UCS4的BMP必然小于0x10000，所以就目前而言，可以认为UTF-16和UCS-2基本相同。但UCS-2只是一个编码方案，UTF-16却要用于实际的传输，所以就不得不考虑字节序的问题。


###UTF的字节序和BOM

&emsp;&emsp;UTF-8以字节为编码单元，没有字节序的问题。UTF-16以两个字节为编码单元，在解释一个UTF-16文本前，首先要弄清楚每个编码单元的字节序。例如收到一个“奎”的Unicode编码是594E，“乙”的Unicode编码是4E59。如果我们收到UTF-16字节流“594E”，那么这是“奎”还是“乙”？

&emsp;&emsp;Unicode规范中推荐的标记字节顺序的方法是BOM。BOM不是“Bill Of Material”的BOM表，而是Byte Order Mark。BOM是一个有点小聪明的想法：

&emsp;&emsp;在UCS编码中有一个叫做"ZERO WIDTH NO-BREAK SPACE"的字符，它的编码是FEFF。而FFFE在UCS中是不存在的字符，所以不应该出现在实际传输中。UCS规范建议我们在传输字节流前，先传输字符"ZERO WIDTH NO-BREAK SPACE"。

&emsp;&emsp;这样如果接收者收到FEFF，就表明这个字节流是Big-Endian的；如果收到FFFE，就表明这个字节流是Little-Endian的。因此字符"ZERO WIDTH NO-BREAK SPACE"又被称作BOM。

&emsp;&emsp;UTF-8不需要BOM来表明字节顺序，但可以用BOM来表明编码方式。字符"ZERO WIDTH NO-BREAK SPACE"的UTF-8编码是EF BB BF（读者可以用我们前面介绍的编码方法验证一下）。所以如果接收者收到以EF BB BF开头的字节流，就知道这是UTF-8编码了。

&emsp;&emsp;Windows就是使用BOM来标记文本文件的编码方式的。

### 关于网络传输各编码流量耗费

*  编码方式&emsp;&emsp;ASCII字符&emsp;&emsp;中文字符
*  UTF-8&emsp;&emsp;&emsp;一个字节&emsp;&emsp;三个字节
*  UTF-16&emsp;&emsp;&emsp;两个字节&emsp;&emsp;两个字节
*  GBK&emsp;&emsp;&emsp;&emsp;一个字节&emsp;&emsp;两个字节

&emsp;&emsp;显然明智之选当然是GBK啦，首先GBK支持中文最好，其次GBK对于中文还有ASCII码字符编码最省，对于中文环境的网络传输来说，GBK当之无愧了。

###测试代码


```
import java.io.UnsupportedEncodingException;
public class Test {
public static void main(String args[]) throws UnsupportedEncodingException {
byte[] utf8 = "A".getBytes("UTF-8");
byte[] utf16 = "A".getBytes("UTF-16");
byte[] gbk = "A".getBytes("GBK");
String aa = new String (utf8, "utf-8");
System.out.println(toHexByte(utf8));
System.out.println(toHexByte(utf16));
System.out.println(toHexByte(gbk));
char temp = (char) (0x0102);
}
public static final String toHexByte(byte[] b) {
String str = "";
for(int i=0;i<b.length; i++) {
str += toHexByte(b[i]);
}
return str;
}
public static final String toHexByte(byte b) {
String str = "" + "0123456789ABCDEF".charAt(0xf & b>>4) + "0123456789ABCDEF".charAt(0xf & b>>0);
return str;
}
public static String toHexBytes(byte[] b) {
StringBuffer sb = new StringBuffer(b.length);
String str ;
for(int i=0;i<b.length;i++) {
int temp = b[i];
str = Integer.toHexString(temp);
if(str.length()<2)
sb.append(0);
sb.append(str.toUpperCase());
}
return sb.toString();
}
}
```

