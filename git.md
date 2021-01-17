首先你需要一个github账号，所有还没有的话先去注册吧！

https://github.com/

我们使用git需要先安装git工具，这里给出下载地址，下载后一路直接安装即可：

https://git-for-windows.github.io/

1.进入Github首页，点击New repository新建一个项目

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE2NDE1NDUyNi00MTg0NDE5OTEucG5n?x-oss-process=image/format,png)

 2.填写相应信息后点击create即可 

Repository name: 仓库名称

Description(可选): 仓库描述介绍

Public, Private : 仓库权限（公开共享，私有或指定合作者）	

Initialize this repository with a README: 添加一个README.md

gitignore: 不需要进行版本管理的仓库类型，对应生成文件.gitignore

license: 证书类型，对应生成文件LICENSE

 

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE2NTAyNTM3MC00OTQ0NDUwNi5wbmc?x-oss-process=image/format,png)

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE2NTEwNzMwOC00Mzk5MjYwMDYucG5n?x-oss-process=image/format,png)

4.点击Clone or dowload会出现一个地址，copy这个地址备用。

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE2NTQ1MDYzNi0xMTY3NzIyMDAzLnBuZw?x-oss-process=image/format,png)

5.接下来就到本地操作了，首先右键你的项目，如果你之前安装git成功的话，右键会出现两个新选项，分别为Git Gui Here,Git Bash Here,这里我们选择Git Bash Here，进入如下界面，Test_Bluetooth即为我的项目名。

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTA0ODMwOC0xOTQ4OTUyOTkucG5n?x-oss-process=image/format,png)

6.接下来输入如下代码（关键步骤），把github上面的仓库克隆到本地

git clone https://github.com/CKTim/BlueTooth.git（https://github.com/CKTim/BlueTooth.git替换成你之前复制的地址）

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTE0Mzk4MC03NDExNTIwNDIucG5n?x-oss-process=image/format,png)

 7.这个步骤以后你的本地项目文件夹下面就会多出个文件夹，该文件夹名即为你github上面的项目名，如图我多出了个Test文件夹，我们把本地项目文件夹下的所有文件（除了新多出的那个文件夹不用），其余都复制到那个新多出的文件夹下，

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTIwOTczMC0yMDg5NDk2NDgzLnBuZw?x-oss-process=image/format,png)

8.接着继续输入命令 cd Test，进入Test文件夹

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTQ1NDI2MS0xMDM2Mzc3Ny5wbmc?x-oss-process=image/format,png)

9.接下来依次输入以下代码即可完成其他剩余操作：

git add .     （注：别忘记后面的.，此操作是把Test文件夹下面的文件都添加进来）

​	  "提交信息"  （注：“提交信息”里面换成你需要，如“first commit”）

git push -u origin master  （注：此操作目的是把本地仓库push到github上面，此步骤需要你输入帐号和密码）

 ![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTU1NzgyMy0zODYzNTEyLnBuZw?x-oss-process=image/format,png)		

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTY1MTgwOC0xMjI1MzM1MTM2LnBuZw?x-oss-process=image/format,png)

![img](https://imgconvert.csdnimg.cn/aHR0cDovL2ltYWdlczIwMTUuY25ibG9ncy5jb20vYmxvZy85MTM5MTMvMjAxNjA4LzkxMzkxMy0yMDE2MDgyMzE3MTgxODkzMy0xNTA2ODE1NTY2LnBuZw?x-oss-process=image/format,png)