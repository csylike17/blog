# JS基础

## 规范

[https://github.com/YuArtian/blog/blob/master/JS%E5%9F%BA%E7%A1%80/ECMA%E7%9A%84%E5%90%84%E7%89%88%E6%9C%AC%E8%A7%84%E8%8C%83.md](https://github.com/YuArtian/blog/blob/master/JS基础/ECMA的各版本规范.md)

## 基础

> https://juejin.im/post/5c2ca1106fb9a049ac794510
>
> https://javascript.ruanyifeng.com/advanced/interpreter.html
>
> https://segmentfault.com/a/1190000013126460

### 执行环境

`JavaScript` 代码的执行要依靠 宿主环境（浏览器，Node，其他的桌面系统等）

宿主环境会通过 JS引擎（V8等）提供 `JavaScript` 的执行环境

在这个执行期环境，首先需要创建一个代码解析的初始环境，初始化的内容包含：

1. 一套与宿主环境相关联系的规则
2. JavaScript 引擎内核（基本语法规则、逻辑、命令和算法）
3. 一组内置对象和 API
4. 其他约定

> 不同的 JavaScript 引擎定义初始化环境是不同的，这就形成了所谓的浏览器兼容性问题，因为不同的浏览器使用不同 JavaScipt 引擎
>
> 微软放弃了自家的 Edge，改用了 Chromium

### 编译原理

以 V8 为例，JS引擎的工作流程如下

<img src="https://github.com/YuArtian/blog/blob/master/Map/V8%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png?raw=true"/>



#### 分词/词法分析（Scanner）

JS 文件只是一个源码（就是一堆字符串），机器是无法执行的，引擎会调用 Scanner 对源码进行词法分析

词法分析 就是把源码的字符串分割出来，生成一系列的 token，也就是 **词法单元（token）**

```
var sum = 30;
// 词法分析后的结果
[
  "var" : "keyword",
  "sum" : "identifier",
  "="   : "assignment",
  "30"  : "integer",
  ";"   : "eos" (end of statement)
]
```

#### 语法分析（Parser）

> https://juejin.im/post/5c2ca1106fb9a049ac794510
>
> 

词法分析完后，接下来的阶段就是使用 Parser 进行语法分析，语法分析的输入就是词法分析的输出

Parser 接收 词法单元流 输出 AST（抽象语法树）

词法分析和语法分析不是完全独立的，而是交错进行的，也就是说，词法分析器不会在读取所有的词法记号后再使用语法分析器来处理。在通常情况下，每取得一个词法记号，就将其送入语法分析器进行分析

<img src=""/>

##### 语法检查

如果 JavaScript 解释器在构造语法树的时候发现有语法错误，就会抛出异常并结束整个代码块的解析

```
<script>
function func() {
  let a = 10;
  var a = 1; //Uncaught SyntaxError: Identifier 'a' has already been declared
}
</script>
```

虽然函数 `func` 没有执行，但是在语法分析阶段就已经检查出错误，并报错了

##### 生成 AST

AST（抽象语法树）指的是源代码语法所对应的树状结构

https://astexplorer.net/ 在线生成AST

例如，var sum = 30；会对应生成如下的AST

```
{
  "type": "Program",
  "start": 0,
  "end": 13,
  "body": [
    {
      "type": "VariableDeclaration",
      "start": 0,
      "end": 13,
      "declarations": [
        {
          "type": "VariableDeclarator",
          "start": 4,
          "end": 12,
          "id": {
            "type": "Identifier",
            "start": 4,
            "end": 7,
            "name": "sum"
          },
          "init": {
            "type": "Literal",
            "start": 10,
            "end": 12,
            "value": 30,
            "raw": "30"
          }
        }
      ],
      "kind": "var"
    }
  ],
  "sourceType": "module"
}
```



##### 确定词法作用域

<img src=""/>

生成 AST 的同时也会确定相应的词法环境结构，也就是常说的作用域



#### 代码生成

将 AST 转换成可执行代码的过程被称为代码生成。这个过程与语言、目标平台相关

##### 解析器（Ignition）

解析器 解析 AST 生成字节码（bytecode），并解释执行字节码



##### 优化编译器（TurboFan）

将字节码（Bytecode）编译生成优化的机器代码（Machine Code）



#### 一条声明语句的执行过程

- V8 引擎进入可执行上下文（一般最开始为全局上下文，函数调用时进入函数上下文）

  使用 Scanner 对代码进行词法分析，分解为词法单元

- 在对当前的整个可执行上下文分析完成后，Parser 将对 token 进行语法分析和语法检查，最终生成AST（抽象语法树）

- 引擎每次遇到声明语句，就会把声明传到 当前词法环境 中创建一个绑定

  每次声明都会为变量分配内存，只是分配内存，并不会修改源代码将变量声明语句提升。

  变量默认值设为 `undefined`

- 在这之后，引擎每一次遇到赋值或者取值，都会到 词法环境  中查找变量

  如果在 当前词法环境 中没有查找到就根据 外部词法引用（outer），接着向 上级词法环境 查找

  找不到就返回 `null`

- 接着引擎生成 CPU 可以执行的机器码

- 最后， 代码执行完毕









### 语句

<img src="https://github.com/YuArtian/blog/blob/master/Map/yuju_1.png?raw=true"/>

<img src="https://github.com/YuArtian/blog/blob/master/Map/yuju_2.jpg?raw=true"/>

#### 表达式

https://time.geekbang.org/column/article/88827

##### 连续赋值

```
a = b = c = d
```

向右结合，等价于

```
a = (b = (c = d))
```

也就是说，先把 d 的结果赋值给 c，再把整个表达式的结果赋值给 b，再赋值给 a

##### 逗号分隔的表达式会顺次执行

整个表达式的结果 就是 最后一个逗号后的表达式结果

```
a = b, b = 1, null;
```

### 声明

#### let const 声明

##### 不存在变量提升

使用 `let` `const` 声明的变量，在声明之前使用该对象，就会报错 `ReferenceError`

let 和 const 声明虽然看上去是执行到了才会生效，但是实际上，它们还是会被预处理

如果当前作用域内有声明，就无法访问到外部的变量

```
const a = 2;
if(true){
    console.log(a); //抛错
    const a = 1;   
}
```

这里在 if 的作用域中，变量 a 声明执行到之前，我们访问了变量 a，这时会抛出一个错误，这说明 const 声明仍然是有预处理机制的

在执行到 const 语句前，我们的 JavaScript 引擎就已经知道后面的代码将会声明变量 a，从而不允许我们访问外层作用域中的 a

##### 暂时性死区（TDZ）

只要块级作用域内存在 let、const 命令，它所声明的变量就“绑定”（binding）这个区域，不再受外部的影响

其本质就是，只要进入当前作用域，所要使用的变量就已经存在，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量

ES6 规定暂时性死区和 let 、const 语句不出现变量提升，主要是为了减少运行时的错误，防止在变量声明前就是用这个变量，从而导致意料之外的行为

##### 不允许重复声明

不允许在相同作用域内，重复声明同一个变量

#### var 声明

var 声明永远作用于脚本、模块和函数体这个级别，在预处理阶段，不关心赋值的部分，只管在当前作用域声明这个变量。会进行变量提升

```
var a = 1;

function foo() {
    console.log(a); //undefined
    if(false) {
        var a = 2;
    }
}

foo();
```

var 的作用能够穿透一切语句结构，它只认脚本、模块和函数体三种语法结构

#### function 声明

在全局（脚本、模块和函数体），function 声明表现跟 var 相似，不同之处在于，function 声明不但在作用域中加入变量，还会给它赋值

function 声明出现在 if 等语句中的情况有点复杂，它仍然作用于脚本、模块和函数体级别，在预处理阶段，仍然会产生变量，它不再被提前赋值

> 内核版本不一样可能会出现不同的结果

```
console.log(foo); //undefined
if(true) {
    function foo(){

    }
}
```

##### arguments

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/arguments

`arguments`对象是所有（非箭头）函数中都可用的 局部变量，不是 `Array` ，可以被设置

可以被转换为数组，但是对参数使用slice会阻止某些JavaScript引擎中的优化

```
var args = Array.prototype.slice.call(arguments);
var args = [].slice.call(arguments);

// ES2015
const args = Array.from(arguments);
const args = [...arguments];

var args = (arguments.length === 1 ? [arguments[0]] : Array.apply(null, arguments));
```



`typeof` 返回 `object`

#### class 声明

class 声明在全局的行为跟 function 和 var 都不一样。在 class 声明之前使用 class 名，会抛错

但是其实 class 声明也是有预处理的

```
var c = 1;
function foo(){
    console.log(c);//报错
    class c {}
}
foo();
```

如果去掉 class 声明，则会正常打印出 1，也就是说，出现在后面的 class 声明影响了前面语句的结果

这说明，class 声明也是会被预处理的，它会在作用域中创建变量，并且要求访问它时抛出错误

class 内部，可以使用 constructor 关键字来定义构造函数。还能定义 getter/setter 和方法

class 默认内部的函数定义都是 strict 模式的



## String方法/实现

## Array方法/实现

## Object方法/实现

##　Number

## JS引擎

[https://github.com/YuArtian/blog/blob/master/JS%E5%9F%BA%E7%A1%80/JS%E5%BC%95%E6%93%8E/JS%E5%BC%95%E6%93%8E.md](https://github.com/YuArtian/blog/blob/master/JS基础/JS引擎/JS引擎.md)

## 可执行环境

[https://github.com/YuArtian/blog/blob/master/JS%E5%9F%BA%E7%A1%80/%E5%8F%AF%E6%89%A7%E8%A1%8C%E4%BB%A3%E7%A0%81%E4%B8%8E%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83.md](https://github.com/YuArtian/blog/blob/master/JS基础/可执行代码与执行环境.md)

- 执行方式=

- 上下文，执行栈=

- 作用域、作用域链=
- 闭包=
- event loop，宏任务，微任务，nodejs事件循环=
- 块级作用域

## 原型链和继承

- 继承



## Promise

- 1000个Promise

## generator

- async/await

  

## 深浅拷贝



## this指向



## new实现

new 的执行过程：

- 以构造器的 prototype 属性（注意与私有字段[[prototype]]的区分）为原型，创建新对象
- 将 this 和调用参数传给构造器，执行
- 如果构造器返回的是对象，则返回，否则返回第一步创建的对象

## 类型转换

## call, bind, apply

## Proxy

## 精度丢失

## 装饰符写法应用

## instanceof

## 事件模型，冒泡机制

## 模块化

#### js模块加载方案

`CommonJS `（服务器） 、`AMD `（浏览器）、`ES6 module`

##### 区别

ES6 模块的设计思想是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。比如，CommonJS 模块就是对象，输入时必须查找对象属性

#### ES6 module

`JavaScript` 有两种源文件，一种叫做脚本，一种叫做模块。这个区分是在 ES6 引入了模块机制开始的，在 ES5 和之前的版本中，就只有一种源文件类型（就只有脚本）。脚本是可以由浏览器或者 node 环境引入执行的，而模块只能由 `JavaScript ` 代码用 `import ` 引入执行

模块和脚本之间的区别仅仅在于是否包含 import 和 export

现代浏览器可以支持用 `script ` 标签引入模块或者脚本，如果要引入模块，必须给 `script ` 标签添加 `type=“module”` 。如果引入脚本，则不需要 `type`

```
<script type="module" src="xxxxx.js"></script>
```

`script ` 标签如果不加 `type="module"`，默认认为我们加载的文件是脚本而非模块，如果我们在脚本中写了 `export`，就会抛错

## 其他

### 指令序言机制

脚本和模块都支持一种特别的语法，叫做指令序言（Directive Prologs）

"use strict"是 JavaScript 标准中规定的唯一一种指令序言

JavaScript 的指令序言是只有一个字符串直接量的表达式语句，它只能出现在脚本、模块和函数体的最前面

### 自动加分号



# 高级js

- 防抖/节流
- 柯里化
- combineReduce
- 模块化标准和实现
- 设计模式
  - 观察者模式实现

- 浏览器原理

  - 浏览器架构，各个进程作用，如何协调工作
  - 内核架构，各个线程作用，如何协调工作
- 渲染过程，加载优化方案
  
  - 回流和重绘
  
  - js defer，sync
    - css preload
    - 各个标签位置影响
    - 阻塞渲染原理
  - webworker和sharedworker原理与应用
- 图层和图层合并

# CSS

- 优先级计算方式
- flex布局方案
- grid布局方案
- 垂直居中，水平居中，居中方案
- img 优化方案，size
- less，sass使用
  - 函数
  - 变量
- 定位，绝对定位，相对定位
- 文档流
- 盒模型
- float
- 清除浮动
- 塌陷和解决塌陷

# HTML

- 语义化
- 标签和元素，行内元素，块级元素
- 通信原理

  - cookie机制、session机制
  - TCP链接原理，3次握手
  - ISO网络模型
  - 缓存机制与缓存方案

# 浏览器原理

[浏览器原理](https://github.com/YuArtian/blog/blob/master/JS基础/浏览器原理/浏览器原理.md)

# 网络通信

## Web网络基础

> 参考
>
> https://mp.weixin.qq.com/s?__biz=MzAxNDYwODEzNw==&mid=2247485663&idx=1&sn=5f74a69b69c1880d32a17e0977554d55&source=41#wechat_redirect
>
> 

### 网络分层

#### 为什么要分层：

1. 各层次之间是独立的

   某一层并不需要知道它的下一层是如何实现的，而仅仅需要知道该层通过层间的接口所提供的服务。这样，整个问题的复杂程度就下降了。也就是说上一层的工作如何进行并不影响下一层的工作，这样我们在进行每一层的工作设计时只要保证接口不变可以随意调整层内的工作方式

2. 灵活性好

   当任何一层发生变化时，只要层间接口关系保持不变，则在这层以上或以下层均不受影响。当某一层出现技术革新或者某一层在工作中出现问题时不会连累到其它层的工作，排除问题时也只需要考虑这一层单独的问题即可

3. 结构上可分割开

   各层都可以采用最合适的技术来实现。技术的发展往往不对称的，层次化的划分有效避免了木桶效应，不会因为某一方面技术的不完善而影响整体的工作效率

4. 易于实现和维护

   这种结构使得实现和调试一个庞大又复杂的系统变得易于处理，因为整个的系统已经被分解为若干个相对独立的子系统。进行调试和维护时，可以对每一层进行单独的调试，避免了出现找不到、解决错问题的情况

5. 能促进标准化工作

   因为每一层的功能及其所提供的服务都已有了精确的说明。标准化的好处就是可以随意替换其中的某一层，对于使用和科研来说十分方便

#### OSI模型（7层）

ISO提出的OSI（Open System Interconnection）模型将网络分为七层

即 物理层、数据链路层、网络层、传输层、会话层、表示层、应用层

OSI模型共分七层：

1. 物理层：用物理手段将电脑连接起来，对应网线、网卡、接口等物理设备

2. 数据链路层：将由物理层传来的未经处理的位数据包装成数据帧，在通信的实体间建立数据链路连接

   - MAC地址：网络中计算机设备的唯一标识，从计算机在厂商生产出来就被十六进制的数标识为MAC 地址
   - 广播：广播可以帮助我们能够知道对方的 MAC 地址

3. 网络层：为数据在结点之间传输创建逻辑链路，通过[路由选择](https://baike.baidu.com/item/路由选择)算法为子网选择最适当的 ip地址，是 **IP协议**工作的地方

   两台计算机之间的通信分为同一子网络和不同子网络之间。怎么判断两台计算机是否在同一子网络（局域网）中？这就是网络层要解决的问题

   - IP协议：IP编址方案、分组封装格式及分组转发规则
   - 子网掩码：由 32 个二进制位组成，用来划分IP地址中哪一部分是网络号，哪一部分是机器号

4. 传输层：向用户提供可靠的端口到端口(End-to-End)服务，处理[数据包](https://baike.baidu.com/item/数据包)错误、数据包次序，以及其他一些关键传输问题。传输层向高层屏蔽了下层数据通信的细节，是**TCP协议**工作的地方

   - UDP协议
   - TCP协议： TCP 三次握手和四次挥手，就是传输层中完成的

5. 会话层：建立起两端之间的会话关系，并负责数据的传送，负责维护两个结点之间的传输链接，以便确保点到点传输不中断，以及管理数据交换等功能

6. 表示层：用于处理在两个通信系统中交换信息的表示方式，主要包括数据格式变换、[数据加密与解密](https://baike.baidu.com/item/数据加密与解密)、[数据压缩](https://baike.baidu.com/item/数据压缩)与恢复等功能

7. 应用层：网络操作系统和具体的应用程序，对应WWW服务器、FTP服务器等应用软件，**HTTP协议** 工作的地方

   - HTTP协议

#### 因特网协议（5层）

因特网协议栈共有五层：应用层、传输层、网络层、链路层和物理层

这也是实际使用中使用的分层方式

#### TCP/IP模型（4层）

应用层、传输层、网络层、数据链路层

### TCP/IP 协议族

**TCP/IP** 是互联网相关的各类协议族的总称

通常使用的网络（包括互联网）是在 `TCP/IP` 协议族的基础上运作的。而 `HTTP ` 属于它内部的一个子集

## HTTP状态码

### 101 Switching Protocol（协议切换）

表示服务器响应客户端升级协议的请求（ `Upgrade` 请求头 ）正在进行协议切换

## 协议升级机制

### <a name="升级到WebSocket">升级到WebSocket</a>

通过升级HTTP或HTTPS连接来实现 WebSocket

对于前端来讲，可以直接使用 [WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) ，大部分都会自动完成

```html
webSocket = new WebSocket("ws://destination.server.ext", "optionalProtocol");
```

或者 `wss://` 

#### Connection & Upgrade（请求头）（必须）

如果需要从头开始创建WebSocket连接，则必须自己处理握手过程。

创建初始HTTP / 1.1会话后，需要通过向[`Upgrade`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Upgrade)和[`Connection`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Connection)标头添加标准请求来请求升级，如下所示：

```html
Connection: Upgrade
Upgrade: websocket
```

#### 其他可选

##### Sec-WebSocket-Extensions（请求头）

WebSocket  扩展

用 `,` 分隔，有参数的字段用 `;` 分隔

```html
Sec-WebSocket-Extensions: superspeed, colormode; depth=16
```

##### Sec-WebSocket-Key（请求）

是一个 Base64 encode的值，这个是浏览器随机生成的，告诉服务器：别忽悠我，我要验证你是不是webSocket客服

##### Sec-WebSocket-Accept（仅响应头）

和 Sec-WebSocket-Key 配合使用，并不能提供安全性，只能防止乱用。

这样的机制可以验证双方都是 socket

##### Sec-WebSocket-Protocol

是一个用户定义的字符串，用来区分相同URL下，不同的服务所需要的协议

##### Sec-WebSocket-Version

说明 WebSocket 版本

### <a name="升级到HTTP/2">升级到HTTP/2</a> <?>

#### http

客户端使用 HTTP `Upgrade` 机制请求升级

HTTP2-Settings 首部字段是一个专用于连接的首部字段，它包含管理 HTTP/2 连接的参数(使用 Base64 编码)，其前提是假设服务端会接受升级请求

```
 GET / HTTP/1.1
 Host: server.example.com
 Connection: Upgrade, HTTP2-Settings
 Upgrade: h2c
 HTTP2-Settings: <base64url encoding of HTTP/2 SETTINGS payload>
```

服务器如果支持 http/2 并同意升级，则转换协议，否则忽略

```
HTTP/1.1 101 Switching Protocols
Connection: Upgrade
Upgrade: h2c
```

目前浏览器只支持 TLS 加密下的 HTTP/2 通信，也就是说，HTTP 不能协商升级到 HTTP/2

#### https

TLS 加密中在 Client-Hello 和 Server-Hello 的过程中通过 [ALPN](https://zh.wikipedia.org/wiki/应用层协议协商) 进行协议协商，可以升级到 HTTP/2

## WebSocket

> WebSocket 是什么原理？为什么可以实现持久连接？ - Ovear的回答 - 知乎 https://www.zhihu.com/question/20215561/answer/40316953



Websocket是一个**持久化**的协议，相对于HTTP这种**非持久**的协议来说

Websocket是基于HTTP协议的，或者说**借用**了HTTP的协议来完成一部分握手

### [升级到WebSocket](#升级到WebSocket)



## 请求分析（Chrome devtool）

https://developers.google.com/web/tools/chrome-devtools/network/understanding-resource-timing

https://developers.google.com/web/tools/chrome-devtools/network/reference

<img src="https://github.com/YuArtian/blog/blob/master/Map/waterfall-hover.png?raw=true"/>

<a href="https://developers.google.com/web/tools/chrome-devtools/network/reference#timing-explanation">名词解释</a>

## 

## HTTP2

> https://developers.google.com/web/fundamentals/performance/http2/?hl=zh-cn
>
> https://http2.akamai.com/demo
>
> HTTP/2 相比 1.0 有哪些重大改进？ - Leo Zhang的回答 - 知乎 https://www.zhihu.com/question/34074946/answer/75364178
>
> HTTP/2 对现在的网页访问，有什么大的优化呢？体现在什么地方？ - Leo Zhang的回答 - 知乎 https://www.zhihu.com/question/24774343/answer/96586977
>
> https://juejin.im/post/5b88a4f56fb9a01a0b31a67e

### HTTP/2 的主要目标

- 通过支持完整的请求与响应复用来减少延迟
- 通过有效压缩 HTTP 标头字段将协议开销降至最低
- 增加对请求优先级和服务器推送的支持

### HTTP/2 主要特点

HTTP/2 没有改动 HTTP 的应用语义。 HTTP 方法、状态代码、URI 和标头字段等核心概念一如往常

不过，HTTP/2 修改了数据格式化（分帧）以及在客户端与服务器间传输的方式

HTTP/2 中，同域名下所有通信都在单个连接上完成，该连接可以承载任意数量的双向数据流

每个数据流都以消息的形式发送，而消息又由一个或多个帧组成

多个帧之间可以乱序发送，根据帧首部的流标识可以重新组装

### 二进制分帧层

在不改动 HTTP/1.x 的语义、方法、状态码、URI 以及首部字段….. 的情况下, HTTP/2 是如何做到「突破 HTTP1.1 的性能限制，改进传输性能，实现低延迟和高吞吐量」的 ?

关键之一就是在 应用层(HTTP/2)和传输层(TCP or UDP)之间增加一个二进制分帧层

新的二进制分帧机制改变了客户端与服务器之间交换数据的方式

- *数据流*：已建立的连接内的双向字节流，可以承载一条或多条消息
- *消息*：与逻辑请求或响应消息对应的完整的一系列帧
- *帧*：HTTP/2 通信的最小单位，每个帧都包含帧头，至少也会标识出当前帧所属的数据流

这些概念的关系总结如下：

- 所有通信都在一个 TCP 连接上完成，此连接可以承载任意数量的双向数据流
- 每个数据流都有一个唯一的标识符和可选的优先级信息，用于承载双向消息
- 每条消息都是一条逻辑 HTTP 消息（例如请求或响应），包含一个或多个帧
- 帧是最小的通信单位，承载着特定类型的数据，例如 HTTP 标头、消息负载等等。 来自不同数据流的帧可以交错发送，然后再根据每个帧头的数据流标识符重新组装

### 多路复用

在一个 TCP 连接上，我们可以向对方不断发送帧，每帧的 stream identifier 的标明这一帧属于哪个流，然后在对方接收时，根据 stream identifier 拼接每个流的所有帧组成一整块数据。 把 HTTP/1.1 每个请求都当作一个流，那么多个请求变成多个流，请求响应数据分成多个帧，不同流中的帧交错地发送给对方，这就是 HTTP/2 中的多路复用

流的概念实现了单连接上多个 请求 - 响应 并行，解决了线头阻塞的问题

减少了 TCP 连接数量和 TCP 连接慢启动造成的问题，合并多个请求为一个的优化也将不再适用

### 首部压缩

HTTP/2 使用了专门为首部压缩而设计的 [HPACK](https://link.zhihu.com/?target=http%3A//http2.github.io/http2-spec/compression.html) 算法

### 服务端推送<?>

> server push 需要服务端设置，并不是说浏览器发起请求，与此请求相关的资源服务端就会自动推送

服务端推送是一种在客户端请求之前发送数据的机制

在 HTTP/2 中，服务器可以对客户端的一个请求发送多个响应

Server Push 让 HTTP1.x 时代使用内嵌资源的优化手段变得没有意义；如果一个请求是由你的主页发起的，服务器很可能会响应主页内容、logo 以及样式表，因为它知道客户端会用到这些东西

#### Sever Push过程<?>

先发送 **PUSH_PROMISE** 帧，来告知客户端要推送的内容。对于要 push 的资源，客户端不再发起请求

#### 如果客户端早已在缓存中有了一份 copy 怎么办<?>

因为 Push 本身具有投机性，所以肯定会出现推送过去的东西浏览器不需要的情况

1. 允许客户端发送 **RESET_STREAM** 主动取消 push <?>

   即使这样，Server-Push 满足条件时便会发起推送，可是客户端已经有缓存了想发送 RST 拒收，而服务器在收到 RST 之前已经推送资源了，虽然这部分推送无效但是肯定会占用带宽

2. Cache Digests <?>

3. 客户端可以限制 PUSH 流的数目，也可以设置一个很低的流量窗口来限制 PUSH 发送的数据大小

### 数据流优先级<?>

HTTP/2 标准允许每个数据流都有一个关联的权重和依赖关系：

- 可以向每个数据流分配一个介于 1 至 256 之间的整数。
- 每个数据流与其他数据流之间可以存在显式依赖关系

### 流控制<?>

流控制是一种阻止发送方向接收方发送大量数据的机制

### 如何升级到 HTTP/2

#### 部署 HTTP/2

> https://zhuanlan.zhihu.com/p/29609078

<img src="https://github.com/YuArtian/blog/blob/master/Map/%E5%A6%82%E4%BD%95%E5%8D%87%E7%BA%A7%E5%88%B0http2.jpg?raw=true"/>

nginx和客户端是HTTP/2，而nginx和业务服务还是HTTP/1.1，因为nginx的服务和业务服务通常是处于同一个内网，速度一般会很快，而nginx和客户端的连接就不太可控了，如果业务服务本身支持HTTP/2，会更好

#### 协商升级

[HTTP/2协商升级](#HTTP/2协商升级)



# Vue

# React

# 工程化

- webpack
- 项目发布流程

# 优化

https://segmentfault.com/a/1190000022205291



# codewar刷题

# leetcode刷题
