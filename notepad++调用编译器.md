#前言
##
notepad++是一款强大的代码编译工具，本文介绍用notepad++调用任意编译器来编译窗口代码。

原理：通过`cmd.exe`调用编译器来对代码进行编译  

**所需工具：**  
>[**notepad++**](https://notepad-plus-plus.org/downloads/ "点击跳转下载页面")  
**编译器(如JAVA，python)**
##正文
打开notepad++，编辑好代码。  

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200202002821.png" width="70%">  

点击运行，运行。  
<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200202003119.png" width="50%">  

在新弹出的窗口中输入下面代码，其中`D:\python\python.exe`为编译器所在的位置，点击保存  
>```cmd /k D:\python\python.exe “$(FULL_CURRENT_PATH)”&PAUSE&EXIT```  

<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200202005853.png" width="30%"> 

设置名字如“以python运行”，设置快捷键，ok
  
<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200202004002.png" width="30%">

以后可以在运行这里快速编译，也可以直接用快捷键快速进行编译  


<img src="https://raw.githubusercontent.com/picgouser/pic/master/20200202004443.png" width="40%">

##小结
对于指令  
```cmd /k D:\python\python.exe “$(FULL_CURRENT_PATH)”&PAUSE&EXIT```  
其中D:\python\python.exe是我计算机中python应用程序的路径，应注意你的路径。其他不变。  
命令解释：  
1. `cmd /k` 的含义是执行后面的命令，并且执行完毕后保留窗口。  
2.`&` 是连接多条命令。  
3.`PAUSE`表示运行结束后暂停，等待一个任意按键。  
4.`EXIT` 表示关闭命令行窗口。如果使用 `cmd /c` 就可以省掉EXIT了。
