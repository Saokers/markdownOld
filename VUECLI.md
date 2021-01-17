## 一、使用之前，我们先来掌握3个东西是用来干什么的。

## npm: Nodejs下的包管理器。

## webpack: 它主要的用途是通过CommonJS的语法把所有浏览器端需要发布的静态资源做相应的准备，比如资源的合并和打包。

## vue-cli: 用户生成Vue工程模板。（帮你快速开始一个vue的项目，也就是给你一套vue的结构，包含基础的依赖库，只需要 npm install就可以安装）

## 开始：

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145017816-1078102167.png)

 

### 如图，下载8.9.3 LTS （推荐给绝大部分用户使用）

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145228394-1659342805.png)

### 双击安装

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145307316-1352109111.png)

### 可以使用默认路径，本例子中自行修改为d:\nodejs

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145332816-1106051339.png)

### 一路点Next

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145443082-1633079415.png)

### 点Finish完成

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145526363-1294346495.png)

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145534144-1685678132.png)

### 打开CMD，检查是否正常

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145657082-1826714804.png)

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145723801-1548672252.png)

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145740222-1613793922.png)

 

### 再看看另外2个目录，npm的本地仓库跑在系统盘c盘的用户目录了(没见到npm-cache是因为没有用过，一使用缓存目录就生成了)，我们试图把这2个目录移动回到D:\nodejs

### 先如下图建立2个目录

###  ![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145811019-407092018.png)

### 然后运行以下2条命令

### npm config set prefix "D:\nodejs\node_global"

### npm config set cache "D:\nodejs\node_cache"

### ![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145854347-808420142.png)

### 如上图，我们再来关注一下npm的本地仓库，输入命令npm list -global

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145924051-372279137.png)

### 输入命令npm config set registry=http://registry.npm.taobao.org 配置镜像站

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212145956347-1443850138.png)

### 输入命令npm config list 显示所有配置信息，我们关注一个配置文件

### C:\Users\Administrator\.npmrc

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150036551-2051372473.png)

### 使用文本编辑器编辑它，可以看到刚才的配置信息

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150059472-912062155.png)

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150115488-275634578.png)

### 检查一下镜像站行不行命令1

### npm config get registry

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150211879-638610714.png)

### 检查一下镜像站行不行命令2

### Npm info vue 看看能否获得vue的信息

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150233535-577937828.png)

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150246691-739368557.png)

 

### 注意，此时，默认的模块D:\nodejs\node_modules 目录

### 将会改变为D:\nodejs\node_global\node_modules 目录，

### 如果直接运行npm install等命令会报错的。

### 我们需要做1件事情：

### 1、增加环境变量NODE_PATH 内容是：D:\nodejs\node_global\node_modules

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150402894-2086448836.png)

 

 

### （注意，一下操作需要重新打开CMD让上面的环境变量生效）

### 一、测试NPM安装vue.js

### 命令：npm install vue -g

### 这里的-g是指安装到global全局目录去

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150420488-1331978539.png)

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150458519-2132301252.png)

### 二、测试NPM安装vue-router

### 命令：npm install vue-router -g

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150538707-640998672.png)

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150544879-938347487.png)

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150744051-1058132537.png)

### 运行npm install vue-cli -g安装vue脚手架

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150805238-349743368.png)

 

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150836707-1925918113.png)

### 编辑环境编辑path

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150853863-1117215657.png)

### 对path环境变量添加D:\nodejs\node_global

### win10以下版本的，横向显示PATH的，注意添加到最后时，不要有分号【;】

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150928332-1178968243.png)

### 重新打开CMD，并且测试vue是否使用正常

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212150949097-1600189468.png)

### 注意，vue-cli工具是内置了模板包括 webpack 和 webpack-simple,前者是比较复杂专业的项目，

### 他的配置并不全放在根目录下的 webpack.config.js 中。

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151033847-2077318030.png)

 

### 初始化，安装依赖

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151121910-1710109810.png)

### 运行npm install安装依赖

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151139176-1075396617.png)

### npm run dev

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151206519-436420011.png)

### 成功界面，提示打开地址http://localhost:8080

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151256129-333658407.png)

 

### 自动打开浏览器http://localhost:8080/#/

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151314504-1422396943.png)

### npm run build

### 生成静态文件，打开dist文件夹下新生成的index.html文件

 

 

### nmp下新建出来的vue01的目录描述：

 

![img](https://images2017.cnblogs.com/blog/1287619/201712/1287619-20171212151528301-530076803.png)