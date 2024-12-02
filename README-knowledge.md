### 1、Node.js简介

#### 1.1、什么是Node.js

简单的说 Node.js 就是运行在服务端的 JavaScript。

Node.js是一个事件驱动I/O服务端JavaScript环境，基于Google的V8引擎

V8引擎执行Javascript的速度非常快，性能非常好。

#### 1.2、Node.js有什么用

如果你是一个前端程序员，你不懂得像PHP、Python或Ruby等动态编程语言，然后你想创建自己的服务，那么Node.js是一个非常好的选择。

Node.js 是运行在服务端的 JavaScript，如果你熟悉Javascript，那么你将会很容易的学会Node.js。

当然，如果你是后端程序员，想部署一些高性能的服务，那么学习Node.js也是一个非常好的选择。

#### 1.3、Node.js安装和查看版本

下载：
```
官网：https://nodejs.org/en/
中文网：http://nodejs.cn/
LTS：长期支持版本
Current：最新版
```
安装和查看版本
```
 node -v
```

#### 1.4、控制台程序
```
console.log('Hello Node.js')
```

**浏览器的内核包括两部分核心：**

- DOM渲染引擎
- js解析器(js引擎)
- js运行在浏览器的内核中的js引擎内部
Node.js 是脱离了浏览器环境运行的JavaScript程序，是基于V8 引擎（Chrome的JavaScript的引擎）

#### 1.5、服务器端应用开发
```
 const http = require('http')
http.createServer(function (request, response){

     // 发送 HTTP 头部 
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200, {'Content-Type': 'text/plain'})
    
    // 发送响应数据 "Hello World"
    response.end('Hello Server')

}).listen(8888)
// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/')
```

运行服务器程序：node 02-server-app.js

服务器启动成功后，在浏览器中输入：http://localhost:8888/ 查看webserver成功运行，并输出html页面

停止服务：ctrl + c

--------------------------------

### 2. NPM简介
#### 2.1、什么是Node.js

- NPM全称Node Package Manager，是Node.js包管理工具，是全球最大的模块生态系统.
- 里面所有的模块都是开源免费的；也是Node.js的包管理工具，相当于后端的Maven 。

#### 2.2、NPM工具的安装位置

我们通过npm 可以很方便地下载js库，管理前端工程。

Node.js默认安装的npm包和工具的位置：Node.js目录\node_modules
_在这个目录(node_modules)下你可以看见 npm目录，npm本身就是被NPM包管理器管理的一个工具，说明 Node.js已经集成了npm工具_
```

在命令提示符输入 npm -v 可查看当前npm版本

npm -v
```

#### 2.3、使用npm管理项目

1.创建文件夹（略）

2.项目初始化
```
#建立一个空文件夹，在命令提示符进入该文件夹  执行命令初始化
npm init
#按照提示输入相关信息，如果是用默认值则直接回车即可。
#name: 项目名称
#version: 项目版本号
#description: 项目描述
#keywords: {Array}关键词，便于用户搜索到我们的项目
#最后会生成package.json文件，这个是包的配置文件，相当于maven的pom.xml
#我们之后也可以根据需要进行修改。
```

```
#如果想直接生成 package.json 文件，那么可以使用命令
npm init -y
```

3.修改npm镜像和仓库

NPM官方的管理的包都是从 http://npmjs.com下载的，但是这个网站在国内速度很慢。

这里推荐使用淘宝 NPM 镜像 http://npm.taobao.org/ ，淘宝 NPM 镜像是一个完整 npmjs.com 镜像，同步频率目前为 10分钟一次，以保证尽量与官方服务同步。

设置镜像地址：
```

经过下面的配置，以后所有的 npm install 都会经过淘宝的镜像地址下载
npm config set registry https://registry.npm.taobao.org 

查看npm配置信息
npm config list

```

设置仓库地址：

默认仓库地址是当前用户目录下，如果当前用户目录有中文，需要修改

```
# 配置全局安装：
npm config set prefix D:\atguigu\node-global

# 配置缓存路径：
npm config set cache D:\atguigu\node-cache

#查看npm配置信息
npm config list

```


4.npm install命令的使用

```
#使用 npm install 安装依赖包的最新版，
#模块安装的位置：项目目录\node_modules
#安装会自动在项目目录下添加 package-lock.json文件，这个文件帮助锁定安装包的版本
#同时package.json 文件中，依赖包会被添加到dependencies节点下，类似maven中的 <dependencies>

npm install jquery

#npm管理的项目在备份和传输的时候一般不携带node_modules文件夹
npm install #根据package.json中的配置下载依赖，初始化项目
#如果安装时想指定特定的版本
npm install jquery@2.1.x

# 局部安装
#devDependencies节点：开发时的依赖包，项目打包到生产环境的时候不包含的依赖
#使用 -D参数将依赖添加到devDependencies节点

npm install --save-dev eslint
#或
npm install -D eslint

#全局安装
#Node.js全局安装的npm包和工具的位置：用户目录\AppData\Roaming\npm\node_modules
#一些命令行工具常使用全局安装的方式
npm install -g webpack
            --global

```

5.其他命令

```
#更新包（更新到最新版本）
npm update 包名

#全局更新
npm update -g 包名

#卸载包
npm uninstall 包名

#全局卸载
npm uninstall -g 包名

```


### 3. 模块化开发（ES5）

1. 模块化产生的背景

随着网站逐渐变成"互联网应用程序"，嵌入网页的Javascript代码越来越庞大，越来越复杂,Javascript模块化编程，已经成为一个迫切的需求。

理想情况下，开发者只需要实现核心的业务逻辑，其他都可以加载别人已经写好的模块。

但是，Javascript不是一种模块化编程语言，它不支持"类"（class），包（package）等概念，更遑论"模块"（module）了。

2. 什么是模块化开发

传统非模块化开发有如下的缺点：1.命名冲突 2.文件依赖
模块化规范： 1. CommonJS模块化规范（ES5模块化规范） 2.ES6模块化规范

**ES5模块化
每个文件就是一个模块，有自己作用域。在一个文件里定义的变量、函数、类，都是私有的，对其他文件不可见。**

3.导出模块
```
// 定义成员：
const sum = function(a,b){
    return parseInt(a) + parseInt(b)
}
const subtract = function(a,b){
    return parseInt(a) - parseInt(b)
}

```

导出模块中的成员
```
// 导出成员：
module.exports = {
    sum: sum,
    subtract: subtract
}

-------------------------
//简写
module.exports = {
    sum,
    subtract
}

```


4.导入模块

创建 common-js模块化/引入模块.js

```
//引入模块，注意：当前路径必须写 ./
const m = require('./四则运算.js')
console.log(m)
const result1 = m.sum(1, 2)
const result2 = m.subtract(1, 2)
console.log(result1, result2)

```

运行程序
```
node common-js模块化/引入模块.js
```

**CommonJS使用 exports 和require 来导出、导入模块。**


### 3. 模块化开发（ES6）

1. ES6简介
   （1）ECMAScript 6.0（以下简称 ES6）是 JavaScript 语言的下一代标准，已经在 2015 年 6 月正式发布了。
       它的目标，是使得 JavaScript 语言可以用来编写复杂的大型应用程序，成为企业级开发语言。

   （2）ECMAScript 和 JavaScript 的关系是，前者是后者的规格，  
       后者是前者的一种实现（另外的 ECMAScript 的实现还有 Jscript 和 ActionScript）

    JavaScript是ECMAScript的一种实现
    ECMAScript是一种关于JavaScript的语言标准



##### 2. ES6模块化写法（一）
ES6使用 export 和 import 来导出、导入模块。

1.导出模块

创建 src/userApi.js
```
export function getList() {
    console.log('获取数据列表')
}
export function save() {
    console.log('保存数据')
}

```

2.导入模块

创建 src/userComponent.js

```

//只取需要的方法即可，多个方法用逗号分隔
import { getList, save } from "./userApi.js"
getList()
save()

```
**注意：这时程序无法运行，因为ES6的模块化无法在Node.js中执行，需要用Babel编辑成ES5后再执行。**

3. 安装Babel

Babel是一个广泛使用的转码器，可以将ES6代码转为ES5代码，从而在现有环境执行。

--- 1.安装命令行转码工具

Babel提供babel-cli工具，用于命令行转码。它的安装命令如下：

```

npm install --global babel-cli
#查看是否安装成功
babel --version

```
--- 2.配置.babelrc

Babel的配置文件是*.babelrc*，存放在项目的根目录下，该文件用来设置*转码规则*和*插件*。

presets字段设定转码规则，将es2015规则加入 .babelrc

```
{
    "presets": ["es2015"],
    "plugins": []
}
```

--- 3.安装转码器

在项目中安装

```
npm install --save-dev babel-preset-es2015
```

--- 4.转码

```

# 整个目录转码
mkdir dist1
# --out-dir 或 -d 参数指定输出目录
babel src -d dist1

```

--- 5. 运行程序
```
node dist1/userComponent.js
```


##### 2. ES6模块化写法（二）
1.导出模块

创建 src/userApi2.js
```
export default {
    getList() {
        console.log('获取数据列表2')
    },
    save() {
        console.log('保存数据2')
    }
}
```

2.导入模块

创建 src/userComponent2.js

```

import user from "./userApi2.js"
user.getList()
user.save()

```

3. 转码
```

# 整个目录转码
mkdir dist2
# --out-dir 或 -d 参数指定输出目录
babel es6 -d dist2

```
4. 运行程序

```

node dist2/userComponent2.js

```