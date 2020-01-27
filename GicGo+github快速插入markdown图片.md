##前言
**仅以此记录本人学习过程**  

本人在使用markdown的时候发现截图插入有点困难<del>饱受网友吐槽</del>，网上只给出了三种插入图片的方法  
1. 通过引用本地图片路径插入图片      
2. 通过引用图片的url链接插入图片      
3. 通过将图片转码插入图片(即将图片转化成base64,再利用markdown自带的解码转化成图片<del>界面令人头秃<del/>)
  
**插入图片方法**[**参考这里**](https://www.jianshu.com/p/280c6a6f2594 "摘自简书用户清风Python") 
  
但是显然各种方法都有利有弊  

于是参考方法二，找到了以下的方法，该方法有以下**优点**：  
1. 避免了方法一不灵活不好分享，本地图片的路径更改或丢失都会造成markdown文件调不出图的缺点  
2. 避免了方法三base64编码占用篇幅大<del>怎一个大字了得？</del>且转换过程不方便  
3. 能在截图后然后通过快捷键自动上传获取图片url，自动构造出markdown语句并置于剪辑板（**用户只需直接粘贴，其他都不用管**）<del>我不是懒</del>。

**所用工具：[markdownpad2]()，[PicGo2.2.2]()，[GitHub]()**
##做法：GicGo+GitHub图床
1. 先下载GicGo，安装最新版本GicGo  
[https://github.com/Molunerfinn/PicGo/releases](https://github.com/Molunerfinn/PicGo/releases)  

2. 在GitHub上创建一个存放图片的仓库
3. 获取token（知道怎样获取的直接跳过）
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128034613.png" width="23%">  
>右上角点击`个人中心`，点击`settings`  
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128034727.png" width="23%">  
>在新出现的页面，点击`developer settings`  
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128034854.png" width="90%">  
>点击`Personal access tokens`  
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128035026.png" width="90%">   
>点击`generate new token`  
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128035339.png" width="90%">  
>选择`repo`，下拉找到`generate token`，点击  
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128040233.png" width="90%">  
>记好这个`token`，离开这个页面就看不到了，**这个token只显示一次！！！**  

4.运行PicGo  
5.图床选择GitHub图床，然后进入图床设置，找到GitHub图床设置  
><img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128040917.png" width="80%">  

6.在PicGo设置那里找到`自定义链接格式`   
7.输入`<img src="$url" width="100%">`，确定  
8.在上传区界面选择`custom链接格式`  
9.设置一个快捷上传的快捷键，本人使用的是`alt`+`z` 
> <img src="https://raw.githubusercontent.com/picgouser/pic/master/20200128045416.png" width="80%">  

10.大功告成，享受飞一般的感觉吧。  
##
疑问：怎样修改图片的大小？  
解答：在markdown编辑区粘贴后，把width="100%"改成自己想要的比例就行。