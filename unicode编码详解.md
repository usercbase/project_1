#前言
##
逛了一天博客，学习了计算机的编码<del>其实是在偷懒</del>。个人理解可能不是十分正确，敬请纠正。


**声明：仅以此作为本人学习过程的记录**
##正文
计算机的编码发展是这样的<del>不是历史啊不要睡着</del>，众所周知计算机的内部是大规模的集成电路，其主要形式就是0和1。那么0和1怎样表示世间万物？这就出现了编码。  


计算机是美国发明的，美国人在制作编码方案的时候是将英文字母以及阿拉伯数字和一些符号对应0到127即 0x00 到 0x7F ，也就是7个bit，制作了一套编码方案。也就是我们说的[ASCII]()。 

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200129224548.png" width="90%"> 

但是后来随着科技发展全世界开始都接触计算机。大部分国家都觉得本国的文字无法表示，于是每个国家都开始制作本国的编码方案。  

当然这里以中国作为主要对象，由于汉字非常多一个字节(8bit)无法满足中国的汉字需求，所以中国的编码方案选择了两个字节作为一个汉字的编码，我们的做法是把两个字节（字符值都大于或等于128）编码为一个汉字。规定每个字节的值在 0x00 到 0x7F （十进制为0到127）按照ASCII编码，其余作为汉字编码。并选取了高字节的 0xA1 到 0xF7 和低字节的 0xA1 到 0xFE 两个字节来编码汉字，这就有7000多字（其中包括本来就出现在ASCII中的句号逗号等，我们称为全角标点，ASCII中的是半角标点）的汉字编码，也就是[GB2312]()编码。  

但中国的汉字远不止这几千个。然后我们干脆把这两个字节的后一字节的 0x00 到 0x7F 抹去，用来给汉字编码（即两个字节只要前面那个字符值大于127就是中文，不管后面那个字符值是否大于127），组成了一个能容纳两万多汉字及符号的编码方案,这就是[GBK]()编码（有兼容GB2312的好处）。  

然后少数民族也加了一点自己的字进去，就成了[GB18030]()。  

我们看着很满意，把他们统称为"DBCS"（Double Byte Charecter Set 双字节字符集）。  

但是与此同时，其他国家也有了他们的编码方案。这就出现了矛盾  
>1. 不同国家的编码方案对应的字不一样，也就是一套二进制的数字由不同的解码方案会出现不一样的结果（经常都是乱码）。
2. 给每套编码方案指定代号然后用对应代号的编码方案来翻译对应的二进制文件。发现编码方案的代号在微软、IBM等公司又不一样。
3. 同时又发现一个更大的问题。就是如果一个文件包含多国文字，那不能同时正常显示。

于是出现了ISO（国际标准化组织）提出了**[unicode]()**（全称"Universal Multiple-Octet Coded Character Set" 统一码），通俗来说就是用一本**超大字典**，包括全世界所有的文字符号（收录了多少字我不知道，盲猜是上百万级别），每个国家的文字在里面都有对应的位置。就像这样  
<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200129040644.png" width="40%">

其中的U+应该就是表示这是unicode编码的意思。  

然后问题又来了，有的字占四个16进制位，有的占五个六个。这样怎样储存为二进制形式，又进行了一系列的讨论...  

最后提出了一系列的方案，其中以utf-8，utf-16，utf-32最为有代表性，下面来讲解一下：  

##utf-8
utf-8 是目前互联网上使用最广泛的一种 unicode 编码方式，它的最大特点就是可变长。它可以使用 1 - 4 个字节表示一个字符<del>充分利用空间啊这样</del>，根据字符的不同变换长度。编码规则如下：

>1. 对于单个字节的字符，第一位设为 0，后面的 7 位对应这个字符的 Unicode 码点。因此，对于英文中的 0 - 127 号字符，与 ASCII 码完全相同。这意味着 ASCII 码那个年代的文档用 UTF-8 编码打开完全没有问题。

>2. 对于需要使用 N 个字节来表示的字符（N > 1），第一个字节的前 N 位都设为 1，第 N + 1 位设为0，剩余的 N - 1 个字节的前两位都设位 10，剩下的二进制位则使用这个字符的 Unicode 码点来填充。

对应关系是这样子的：

<table>
<thead>
<tr><th>Unicode 十六进制码点范围</th><th>UTF-8 二进制</th></tr>
</thead>
<tbody>
<tr>
<td>0000 0000 - 0000 007F</td>
<td>0xxxxxxx</td>
</tr>
<tr>
<td>0000 0080 - 0000 07FF</td>
<td>110xxxxx 10xxxxxx</td>
</tr>
<tr>
<td>0000 0800 - 0000 FFFF</td>
<td>1110xxxx 10xxxxxx 10xxxxxx</td>
</tr>
<tr>
<td>0001 0000 - 0010 FFFF</td>
<td>11110xxx 10xxxxxx 10xxxxxx 10xxxxxx</td>
</tr>
</tbody>
</table>

下面以汉字“汉”为利，说明一下如何进行 UTF-8 编码和解码。

“汉”的 Unicode 码点是 0x6c49（110 1100 0100 1001），通过上面的对照表可以发现，0x00006c49 位于第三行的范围（即应该用三个字节来储存），那么得出其格式为 1110xxxx 10xxxxxx 10xxxxxx。接着，从“汉”的二进制数最后一位开始，从后向前依次填充对应格式中的 x，多出的 x 用 0 补上。这样，就得到了“汉”的 UTF-8 编码为 11100110 10110001 10001001，转换成十六进制就是 0xE6 0xB7 0x89。

解码的过程也十分简单：如果一个字节的第一位是 0 ，则说明这个字节对应一个字符；如果一个字节的开头部分大于两个1，那么连续有多少个 1，就表示该字符占用多少个字节。


##utf-16

utf-16的编码规则比较简单：用2或4个字节表示一个字符。然而生硬的理解utf-16有点难<del>令人头秃</del>，我们用抽象的思维理解一下（涉及一点空间思维）。

我们把这本有对应关系的unicode编码表理解为一本超级大字典，这本字典上面有很多页。为了便于理解，我们规定每页只能存放65536（2^16）个这样的对应的关系。目前，一共有17（2^5）页<del>不厚的</del>。也就是说，整个 Unicode 字符集的大小现在是 2^21。

其中第一页（专业领域称为基本平面（简称BMP））码点范围是从 0 到 2^16-1，写成 16 进制就是从 U+0000 到 U+FFFF。存放了最常见的字符，剩下的字符放在其他页（专业领域称为辅助平面（简称SMP）），码点范围从 U+010000 到 U+10FFFF。

utf-16的编码规则很简单：第一页的字符占2个字节，其他页的占4个字节。也就是说，UTF-16 的编码长度要么是 2 个字节（U+0000 到 U+FFFF），要么是 4 个字节（U+010000 到 U+10FFFF）。那么问题来了，当我们遇到两个字节时，到底是把这两个字节当作一个字符还是与后面的两个字节一起当作一个字符呢？


这里体现了utf-16的巧妙性，在第一页中，从 U+D800 到 U+DFFF 是一个空段，即这些码点不对应任何字符，而用来映射其他页的字符。

其他页（辅助平面）的字符位共有 2^20 个，因此表示这些字符至少需要 20 个二进制位。UTF-16 将这 20 个二进制位分成两半，前 10 位映射在 U+D800 到 U+DBFF（二进制为从‭1101100000000000‬到‭1101101111111111‬），称为高位（H），后 10 位映射在 U+DC00 到 U+DFFF（二进制为‭从1101110000000000到‭‭1101111111111111‬‬‬），称为低位（L）。意思是，其他页的一个字符由第一页两个字符表示（是不是有点像上面提到的GB2312标准）。

因此，当我们遇到两个字节，发现它的码点在 U+D800 到 U+DBFF 之间，就可以断定，紧跟在后面的两个字节的码点，应该在 U+DC00 到 U+DFFF 之间，这四个字节必须放在一起解读。

接下来，以汉字"𠮷"为例，说明 UTF-16 编码方式是如何工作的。

汉字"𠮷"的 Unicode 码点为 0x20BB7，该码点显然超出了第一页的范围（0x0000 - 0xFFFF），因此需要使用四个字节表示。首先用 0x20BB7 - 0x10000 计算出超出的部分，然后将其用 20 个二进制位表示（不足前面补 0 ），结果为0001000010 1110110111。接着，将前 10 位映射到 U+D800 到 U+DBFF 之间，后 10 位映射到 U+DC00 到 U+DFFF 即可。U+D800 对应的二进制数为 1101100000000000，直接填充后面的 10 个二进制位即可，得到 1101100001000010，转成 16 进制数则为 0xD842。同理可得，低位为 0xDFB7。因此得出汉字"𠮷"的 UTF-16 编码为 0xD842 0xDFB7。

>Unicode3.0 中给出了辅助平面字符的转换公式：

>H = Math.floor((c-0x10000) / 0x400)+0xD800

>L = (c - 0x10000) % 0x400 + 0xDC00  

>根据编码公式，可以很方便的计算出字符的 UTF-16 编码。

##utf-32

utf-32的编码方式是将所有的字符以四个字节来储存，这种方式解码简单，但文件相对较大。
*****
####资料参考：
[https://www.cnblogs.com/gavin-num1/p/5170247.html](https://www.cnblogs.com/gavin-num1/p/5170247.html)  
[https://www.cnblogs.com/crazylqy/p/10184291.html](https://www.cnblogs.com/crazylqy/p/10184291.html)  
[http://www.360doc.com/content/12/0509/12/1073512_209708141.shtml](http://www.360doc.com/content/12/0509/12/1073512_209708141.shtml)  
[https://segmentfault.com/a/1190000013418463](https://segmentfault.com/a/1190000013418463)