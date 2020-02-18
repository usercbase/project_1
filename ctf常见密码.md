##前言
仅以此作为本人学习过程的记录


##目录
<ol>
<li>
<p>常见编码:</p>
<ol>
<li>ASCII编码</li>
<li>Base64/32/16编码</li>
<li>shellcode编码</li>
<li>Quoted-printable编码</li>
<li>XXencode编码</li>
<li>UUencode编码</li>
<li>URL编码</li>
<li>Unicode编码</li>
<li>Escape/Unescape编码</li>
<li>HTML实体编码</li>
<li>敲击码(Tap code)</li>
<li>莫尔斯电码(Morse Code)</li>
<li>编码的故事</li>
</ol></li>
<li>
<p class="no-text-indent">各种文本加密</p>
</li>
<li>
<p>换位加密:</p>
<ol>
<li>栅栏密码(Rail-fence Cipher)</li>
<li>曲路密码(Curve Cipher)</li>
<li>列移位密码(Columnar Transposition Cipher)</li>
</ol></li>
<li>
<p>替换加密:</p>
<ol>
<li>埃特巴什码(Atbash Cipher)</li>
<li>凯撒密码(Caesar Cipher)</li>
<li>ROT5/13/18/47</li>
<li>简单换位密码(Simple Substitution Cipher)</li>
<li>希尔密码(Hill Cipher)</li>
<li>猪圈密码(Pigpen Cipher)</li>
<li>波利比奥斯方阵密码（Polybius Square Cipher)</li>
<li>夏多密码(曲折加密)</li>
<li>普莱菲尔密码(Playfair Cipher)</li>
<li>维吉尼亚密码(Vigen&egrave;re Cipher)</li>
<li>自动密钥密码(Autokey Cipher)</li>
<li>博福特密码(Beaufort Cipher)</li>
<li>滚动密钥密码(Running Key Cipher)</li>
<li>Porta密码(Porta Cipher)</li>
<li>同音替换密码(Homophonic Substitution Cipher)</li>
<li>仿射密码(Affine Cipher)</li>
<li>培根密码(Baconian Cipher)</li>
<li>ADFGX和ADFGVX密码(ADFG/VX Cipher)</li>
<li>双密码(Bifid Cipher)</li>
<li>三分密码(Trifid Cipher)</li>
<li>四方密码(Four-Square Cipher)</li>
<li>棋盘密码（Checkerboard Cipher)</li>
<li>跨棋盘密码(Straddle Checkerboard Cipher)</li>
<li>分组摩尔斯替换密码(Fractionated Morse Cipher)</li>
<li>Bazeries密码(Bazeries Cipher)</li>
<li>Digrafid密码(Digrafid Cipher)</li>
<li>格朗普雷密码(Grandpr&eacute; Cipher)</li>
<li>比尔密码(Beale ciphers)</li>
<li>键盘密码(Keyboard Cipher)</li>
</ol></li>
<li>
<p>其他有趣的机械密码:</p>
<ol>
<li>恩尼格玛密码</li>
</ol></li>
<li>
<p>代码混淆加密:</p>
<ol>
<li>asp混淆加密</li>
<li>php混淆加密</li>
<li>css/js混淆加密</li>
<li>VBScript.Encode混淆加密</li>
<li>ppencode</li>
<li>rrencode</li>
<li>jjencode/aaencode</li>
<li>JSfuck</li>
<li>jother</li>
<li>brainfuck编程语言</li>
</ol>




**下面整理了部分，其他的不定时更新**

##整理


<table>
<thead>
<tr>
<td><strong>加密方式</strong></td><td><strong>密码形式</strong></td><td><strong>备注</strong></td>
<tr>
</htead>
<tbody>
<tr>
<td>base64</td><td>ZmxhZ3toZWxsb193b3JsZCF9IAo==</td><td>密文后面有时会有=</td>
</tr>
<tr>
<td>base32</td><td>MFZW6ZLXGV2DMNI=</td><td>密文只有大写字母和数字2-7，有时会有=</td>
</tr><tr>
<td>base16</td><td>73647734676E7361</td><td>密文只有大写字母A-F和数字0-9，没有=</td>
</tr><tr>
<td>栅栏密码</td><td>CnN1haOi_.</td><td>解密组数一般为2-30</td>
</tr><tr>
<td>MD5</td><td>5aa765d61d8327de</td><td>长度只有16位或者32位</td>
</tr><tr>
<td>unicode</td><td>\u4e2d\u56fd\u725b\u903c</td><td></td>
</tr><tr>
<td>UrlEncode</td><td>%7b%7d%5b%5d%5c%27%2b%2c%3b</td><td></td>
</tr><tr>
<td>UrlDncode</td><td>&# 76;&# 122;&# 69;&# 120;&# 79;&# 83;&# 56;&# 120;& #77;&#6 8;&# 69;</td><td></td>
</tr><tr>
<td>utf-8</td><td>& #x4E2D;& #x56FD;& #x725B;& #x903C;</td><td></td>
</tr><tr>
<td>摩斯电码</td><td>-.-. .... .. -. .-  -. ---   </td><td></td>
</tr><tr>
<td>摩斯电码变形</td><td>0010 0100 1111011 11 11111 010 000 0 001101 1010 111 100 0 001101 01111</td><td>由0和1组成，每段长度不一样</td>
</tr><tr>
<td>OOK</td><td>Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook! Ook? Ook! Ook! Ook. Ook? Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook.
Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook. Ook? Ook. Ook? Ook! Ook. Ook? Ook.
Ook. Ook! Ook. Ook? Ook.</td><td>https://www.splitbrain.org/services/ook</td>
</tr><tr>
<td>brainfuck</td><td>++++++++[>>+++++++++++<<<<<<<<<-]>>>>>>>+.+.+.</td><td>只有+ >   <  .  ] [ -，8种符号</td>
</tr><tr>
<td>凯撒密码</td><td>dvi</td><td>不断位移直到找到正确的</td>
</tr><tr>
<td>核心价值观编码</td><td>公正民主公正文明公正和谐公正自由公正平等公正公正公正法治</td><td></td>
</tr><tr>
<td>sha1</td><td>dc850856ba5cb68a2980cc1937dd968cd77b8bc5</td><td>跟MD5相似，由MD4发展来，长度为40位</td>
</tr><tr>
<td>当铺密码</td><td>石火王夫井工夫口由中</td><td>只有数字1-9，汉字中有多少个出头，对应数字几，如井对应8，火对应5</td>
</tr><tr>
<td>rot13</td><td>sef</td><td>凯撒位移13位，别称有对称密码</td>
</tr><tr>
<td>jother</td><td>(![]+[])[+[]]+(![]+[])[!+[]+!+[]]+(![]+[])[+!+[]]+(![]+[+[]]+([]+[])[([][(![]+[])[+[]]+([![]]+[][[]])[+!+[]+[+[]]]+(![]+</td><td>F12拉进console中回车自动解吗</td>
</tr><tr>
<td>猪圈密码</td><td><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200218150628.png" width="20%"></td><td></td>
</tr><tr>
<td>培根密码</td><td>aaaaaaaaabaaabaaaabbaabaa</td><td>只由a和b，或者A和B组成</td>
</tr><tr>
<td>aaencode</td><td>(ﾟДﾟ).ﾟΘﾟﾉ=(ﾟДﾟ+ ﾟｰﾟ)[o^_^o -(ﾟΘﾟ)];(oﾟｰﾟo)=(ﾟωﾟﾉ +'_')[c^_^o];(ﾟДﾟ) [ﾟoﾟ]='\"';(ﾟДﾟ) ['_'] ( (ﾟДﾟ) ['_'] (ﾟεﾟ+(ﾟДﾟ)[ﾟoﾟ]+ (ﾟДﾟ)[ﾟεﾟ]+((o^_^o) +(o^_^o))+ (c^_^o)+ (ﾟДﾟ)[ﾟoﾟ]) (ﾟΘﾟ)) ('_');</td><td></td>
</tr><tr>
<td>jjencode</td><td>$.$;$.$$=$.$+(!""+"")[$._$$]+$.__+$._+$.$+$.$$;$.$=($.___)[$.$_][$.$_];$.$($.$($.$$+"\""+$.$_$_+"\"")())();</td><td></td>
</tr><tr>
<td></td><td></td><td></td>
</tr><tr>
<td></td><td></td><td></td>
</tr><tr>
<td></td><td></td><td></td>
</tr>
</tbody>
</table>

##致谢
参考来自  
[https://blog.csdn.net/vhkjhwbs/article/details/99692399](https://blog.csdn.net/vhkjhwbs/article/details/99692399)   
[https://www.cnblogs.com/mq0036/p/6544055.html](https://www.cnblogs.com/mq0036/p/6544055.html)  
侵权请联系删除

