#前言
****
**仅以此作为本人学习过程的记录**

开始学php的时候很多人都很心烦，因为安装apache、mysql、PHP等一系列的软件对于我们新手来说比较头痛，所以我记录一下本人的环境搭建过程。

所需工具：  
[phpstudy_pro](https://www.xp.cn)(集成了php、mysql、apache等环境)  
[IntelliJ IDEA](https://www.jetbrains.com/idea/download/)（自己觉得不错的打码软件，安利一波）
##正文
1.下载phpstudy_pro安装好，运行。  

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200210023853.png" width="70%">  

2.启动apache、FTP、mysql服务（一键式是不是特别方便），启动过程可能有一些问题，可以自行百度。像下图这样就是正常的  

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200210024123.png" width="70%">  

3.以上安装就已经完成了，但是如果想让自己的开发更加方便，推荐安装一下IntelliJ IDEA（PS：网上有汉化包和破解补丁），这是一个支持PHP、HTML、CSS、JS以及JAVA、PYTHON等很多语言的强大集成环境。个人觉得如果单是用来开发php比Eclipse这些好用很多倍。毕竟可以右上角一键运行php文件（本人用的是2016.02.05版的好像高版本的取消了这个功能？？？）
>需要安装php插件，可以参考这里[https://blog.csdn.net/xujialei0704/article/details/81071580](https://blog.csdn.net/xujialei0704/article/details/81071580)

4.想要可视化操作mysql数据库？可以在phpstudy_pro里面下载可视化的数据库管理工具，个人推荐另外下载Navicat for MySQL，挺好用的
>收费？可以参考这里[https://www.52pojie.cn/forum.php?mod=viewthread&tid=729216](https://www.52pojie.cn/forum.php?mod=viewthread&tid=729216)
>>Navicat for MySQL下载完后需要连接本地的mysql，创建链接  
>><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200210033433.png" width="80%">  
>><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200210033710.png" width="80%">  
>>然后连接到本地数据库，即可以对数据库进行可视化操作

5.想要在cmd操作数据库？直接打开cmd然后操作。
>不会添加环境变量，可以参考这里[https://blog.51cto.com/14113984/2428739](https://blog.51cto.com/14113984/2428739)

6.然后一个php开发环境就这样完成了

##总结
phpstudy_pro是必须下载的。其他安利的软件可以不用理会（因为可以直接在phpstudy的WWW文件下创建php文件然后直接从浏览器访问）

也有的网友是用phpstudy+phpstorm搭建的。也可以参考他们的

上面摆放的链接有侵权或者其他问题的，请联系本人删除。