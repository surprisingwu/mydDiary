# 慕课网http协议
#### 经典五层模型

* 应用层(`HTTP,FTP...`): 为应用软件提供了很多服务,构建与`TCP`协议之上,屏蔽网络传输相关细节
* 传输层(`TCP,UDP`): 向用户提供可靠的端到端(`End-to-End`)服务,传输层向高层屏蔽了层数数据通信的细节
* 网络层: 为数据在结点之间传输创建逻辑链路
* 数据链路层: 在通信的实体间建立数据链路链接
* 物理层: 定义物理设备如何传输数据

#### `URI, URL, URN`

* `URI`:  统一资源标识符. 用来标识互联网上的信息资源,包括`URL,URN`
* `URL`: 统一资源定位器.
	* `http://user:pass@host.com:80/path?query=string#hash`
	* 协议+域名+端口+路径+查询字符串+片段标识符
* `URN`: 永久统一资源定符.在资源移动之后还能被找到.目前还没有成熟的使用方案

####  跨域请求的限制

* 跨域请求不需要预请求验证的请求方法: `get, head,post`
* 允许(不需要预请求验证)设置的请求头: 
	* `text/plain`
	*   `multipart/form-data`
	* `application/x-www-from-urlencoded`
* 其它限制: 
	*  请求头限制
		* `XMLHttpRequestUpload`对象均没有注册任何事件监听器
		* 请求中没有使用`ReadableStream`对象
	* 上面的限制可以通过后台设置响应头来处理


```JavaScript
// 这里假如后台使用的node

res.writeHead(200, {
    'Access-Control-Allow-Origin': '*',
    'Access-Control-Allow-Headers': '允许设置的请求头',
    'Access-Control-Allow-Methods': '允许请求的方法,如put,delete等',
    'Access-Control-Max-Age': ''
})
```

#### 内容响应

* `MIME`
	*  `Content-type`: 浏览器通过该值决定采用不同的渲染方式, 这个值简称为`MIME`值
	*  除了`MIME`值外, `Content-type`的值还可以包含一些参数,如字符集

```javascript
  Content-type: text/javascript;charset=utf-8
```

* 附件下载
	* `Content-Disposition`:  客户端根据该字段的值,判断是应该讲报文数据当做是即时浏览的内容,还是可下载的附件
	*  `inline`:  即时查看
	*  `attachment`:  数据可以存为附件. 还可以通过参数指定保存时应该使用的文件名
     
```javascript
	{
    		Content-Disposition: attachment;filename="filename.ext"
	}
```  

* 响应`json`
	* 为了快捷地响应`json`数据,可以进行如下封装

```javascript
  res.json = function (json) {
      res.setHeader('Content-Type','application/json')
      res.writeHead(200)
      res.end(JSON.stringify(json))
  }
```

* 响应跳转
	* 当我们的`URL`因为某些问题(譬如权限限制)不能处理当前请求,需要将用户跳转到别的`URL`, 简单的封装如下

```javascript
  res.redirect = function(url){
      res.setHeader('Location',url)
      res.writeHead(302)
      res.end('Redirect to' + url)
  }
```

#### `Cache-Control`

> 通用消息头字段被用于在`http`请求和响应中通过指定指令来实现缓存机制. 缓存指令是单向的,这意味着在请求设置的指令,在响应中不一定包含相同的指令  

* 指令:
	*  `public`: 表明响应可以被任何对象(包括: 发送请求的客户端,代理服务器等)缓存
	*  `private`: 表明响应只能被单个用户缓存, 不能作为共享缓存(即代理服务器缓存它),可以缓存响应的内容
	*  `no-cache`: 在释放缓存副本之前,强制告诉缓存将请求提交给原始服务器进行验证
	* `only-if-cached`:  表明客户端只接受已缓存的响应, 并且不要向原始服务器检查是否有更新的拷贝
* 到期
	*  `max-age=<seconds>`:  设置缓存存储的最大周期,超过这个时间缓存被认为过期(单位秒)
	* `s-maxage=<seconds>`: 覆盖`max-age`或者`Expires`头,但是仅用于共享缓存(比如个人代理), 并且私有缓存中他被忽略
	*  `max-stale[=<seconds>]`: 表明客户端愿意接收一个已经过期的资源. 可选的设置一个时间(单位秒),表示响应不能超过的过时时间
* 重新验证和重新加载
	* `must-revalidate`: 缓存必须在使用之前验证旧资源的状态,并且不可使用过期资源
	* `proxy-revalidate`: 与`must-revaidate`作用相同,但它仅适用于共享缓存(例如代理), 并被私有缓存忽略
* 其它
	* `no-store`:   缓存不应存储有关客户端请求或服务器响应的任何内容
	* `no-transform`:   不得对资源进行转换或转变


```javascript
import http from 'http'

let server = http.createServer((req, res) => {
  res.setHeader('Cache-Control', 'public, max-age=86400')
  res.end('harttle.land')
})

server.listen(3333)
```

![](%E6%85%95%E8%AF%BE%E7%BD%91http%E5%8D%8F%E8%AE%AE/%E4%BB%A3%E7%90%86%E7%A4%BA%E6%84%8F%E5%9B%BE.png)

#### `HTTP`请求的组成 部分

* 请求报文的组成部分
	* 请求行:  包含请求的方法,`URI`和`http`的版本
	* 请求头:  头部的一些信息
	* 请求体: 请求的查询参数

```javascript
// 用curl模拟的http请求
* Rebuilt URL to: http://localhost:8000/?name=wyt&age=18
*   Trying ::1...
* TCP_NODELAY set
* Connected to localhost (::1) port 8000 (#0)
> GET /?name=wyt&age=18 HTTP/1.1     // 请求行
> Host: localhost:8000              // 请求头
> User-Agent: curl/7.54.0
> Accept: */*
>
> name: wyt // 查询参数,浏览器可以看到 这里是自己加的
> age: 18   
```
* 响应报文的组成部分
	* 响应行:  版本号,状态码,
	* 响应头: 响应的头部信息
	* 响应体: 服务器返回的数据

```javascript
< HTTP/1.1 200 OK    // 状态行
< Set-Cookie: isVisit=1   // 响应头
< Content-Type: text/json
< Date: Mon, 10 Dec 2018 01:58:53 GMT
< Connection: keep-alive
< Transfer-Encoding: chunked
<
* Connection #0 to host localhost left intact
{"name":"wyt","age":"18"}%    
```

##### 缓存验证

> 主要通过`Last-Modified`和`Etag`来进行验证  

* `Last-modified`: 上次修改时间. 一般主要配合下面的俩个字段来验证
	* `If-Modified-Since`或者 `If-Unmodified-Since`, 服务器拿到请求头中这个字段的信息, 会对比上次修改时间以验证资源是否需要更新
* `Etag`: 数据签名(最常见的是对资源内容进行计算生成一个`hash`值)
	* 配合`If-Match`或者`If-Non-Match`使用,对比资源的签名判断是够使用缓存

```javascript

import http from 'http'

let server = http.createServer((req, res) => {
  console.log(req.url, req.headers['if-modified-since'])

// 这是请求设置的请求头
  if (req.headers['if-modified-since']) {
    // 检查时间戳
    res.statusCode = 304
    res.end()
  }
  else {
// 服务器设置的响应头
    res.setHeader('Last-Modified', new Date().toString())
    res.end('harttle.land')
  }
})

server.listen(3333)

// 使用etag

import http from 'http'

let server = http.createServer((req, res) => {
  console.log(req.url, req.headers['if-none-match'])
// 前端设置的请求头
  if (req.headers['if-none-match']) {
    // 检查文件版本
    res.statusCode = 304
    res.end()
  }
  else {
// 服务端设置的响应头
    res.setHeader('Etag', '00000000')
    res.end('harttle.land')
  }
})

server.listen(3333)
```

#### `Cookie`
> 在请求服务器的时候, 服务器设置的保存在客户端的一些用户的信息, 并且在同域的请求会携带.   
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
* `document.cookie`: 前端可以通过这个拿到`cookie`信息,进行修改和使用* 不能跨域设置`Cookie`,在同一个主域名下面,子域名是共享`cookie`的
* `Cookie`具有不可跨域名性

```javascript
// 设置cookie
function setCookie(name,value){
	var Days = 30;
	var exp = new Date();
	exp.setTime(exp.getTime() + Days*24*60*60*1000);
	document.cookie = name + "="+ escape (value) + ";expires=" + 	exp.toGMTString();
}

// 读取cookie

function getCookie(name){
	var arr,reg=new RegExp("(^| )"+name+"=([^;]*)(;|$)");
	if(arr=document.cookie.match(reg)) return unescape(arr[2]);
	else return null;
}

// 删除cookies
function delCookie(name){
	var exp = new Date();
	exp.setTime(exp.getTime() - 1);
	var cval=getCookie(name);
	if(cval!=null)
	document.cookie= name"="+cval+";expires="+exp.toGMTString();
}
```

#### `Session`
* `Session`机制是一种服务端机制, 服务器使用一种类似散列表的解构来保存用户信息(`sessionID`).  服务器为每一个用户生成一个`sessionID`,客户端根据这个`sessinID`来查找用户的信息
* 保存`SessionID`的方式
	* 使用`cookie`.此时`cookie`只是作为载体,存储`sessionID`
	* `URL`重写. `cookie`可以被人为禁止,为了保证`sessionID`能够传递给服务器,使用`URL`重写也是一种解决方法
		* `href="<%=response.encodeURL("index.jsp")%"`,写好后是这样的`href="idnex.jsp;sessionid=jfjdsfajdfjafljafj"`
	* 隐藏表单提交.将`sessionID`放在隐藏表单中
	
#### `Cookie和Session`的区别
* `Cookie`数据存放在客户的浏览器(本地),`Session`数据放在服务器上
* `Cookie`不如`Session`安全
* `Session`: 会在一定的时间内保存在服务器上(访问增多,会占用较多的服务器资源)
* 单个的`cookie`保存的数据不能超过`4k`,为了性能考虑,`cookie和session`都应当短小精悍
* 使用cookie和session的具体场景
	* 一般来说,登陆验证信息,客户的私人信息,如姓名,电话等,应该放在Session中.Cookie则用于用户登陆网站时的自动登陆以及类似"购物车"的处理.使用Cookie保存信息时最好通过加密形式来保存数据,同时是否保存登陆信息,需要由用户自行选择

#### 长连接
* `Http`的长连接和短连接本质上是`TCP`长连接和短连接.`HTTP`属于应用层协议,再传输层使用`TCP`协议,在网络层使用`IP`协议.
	* `IP`协议: 主要解决网络路由和寻址问题
	* `TCP`协议: 主要解决如何在`IP`层之上可靠地传递数据包,是一种面相连接的,可靠的,基于字节流的传输层通信协议
* 长连接和短连接的优缺点: 
	* 长连接可以省去较多的`TCP`建立和关闭的操作,减少浪费,解约时间
	* `client`与`server`之间的连接如果一直不关闭的话, 会存在一个问题,随着客户端连接增多,服务端压力比较大
	* 短连接:  再`TCP`的建立和关闭操作上浪费时间和带宽

#### 数据协商

> 所谓的数据协商,就是客户端和服务端协商传输的数据格式,采用什么格式压缩等  
* `Accept`
	* `Accept-Charset`: `utf-8,iso-8859-1;1=0.5,*;q=0.1` 可以接受的编码格式
	* `Accept-Encoding`: `gzip,deflate,br;q=0.5,*` 可以接受的压缩格式
	* `Accept-Language`: `zh-CN,zh;q=0.9,en;q=0.8`可以接受的语言
* `User-Agent`: `Mozilla/<version>(<system-information>)<platform>(<platform>(<platform-details>)<extensions>`客户端相关的信息
* 服务端可以设置的返回的数据格式, 告诉客户端实际返回的内容类型. 浏览器会在某些情况下进行`MIME`查找,并不一定遵循此标题的值,为了防止这种行为,可以将标题`X-Content-Type-Options: nosniff`
	* `Content-Type`: `media-type;charset;boumdary` 实体头部用于指示资源的`MIME`类型`media type`
		* `MIME types`:  `type/subtype`
			* `text/plain|html|javascript|css`
			* `image/jpeg|png`
			* `audio/mpeg|ogg`|*
			* `video/mp4`
			* `application/json|ecmascript|...`
	* `Content-Encoding`:   表示消息主体进行了何种方式的内容编码转换,这个消息首部用来告知客户端应该怎么解码,才能获取再`Content-Type`中指示的媒体类型的内容
	* `Content-Language`:  用来说明访问者希望采用的语言或语言组合,返回的语言类型

#### Redirect
* `statuscode 301`:  被请求的资源已永久移动到新位置,并且将来任何对此资源的引用都应该使用本响应返回的若干个`URI`之一
	* 永久重定向
* `statuscode 302`:   请求的资源现在临时从不同的`URI`响应请求
	* 临时重定向
* 俩者的区别: 
	* `302`重定向只是暂时的重定向，搜索引擎会抓取新的内容而保留旧的地址，因为服务器返回`302`，所以，搜索搜索引擎认为新的网址是暂时的。
	*   而`301`重定向是永久的重定向，搜索引擎在抓取新的内容的同时也将旧的网址替换为了重定向之后的网址。

```JavaScript
res.writeHead(302, {
    'Location': 'url' 
})
```

#### `Content-Security-Policy`: 内容安全策略

> 主要目标是减少和报告`XSS`攻击,通过制定有效域(浏览器认可执行脚本的有效来源,是服务器管理者有能力减少或消除`XSS`攻击所依赖的载体)  

* 服务端设置响应头: `Content-Security-Policy: default-src 'self'`
* 也可以前端设置`meta标签`
	* `<meta http-equiv="Content-Security-Policy" content="default-src 'self'; img-src https://*; child-src 'none';">`

* `Content-Security-Policy: policy`
	* `policy`: 包含了各种描述的`CSP`策略指令的字符串
* 限制资源获取:
	* `script-src`: 防止内联脚本运行
	* 指定资源类型
		* 资源类型: `connect-src img-src style-src script-src font-src media-src frame-src manifest-src ...`页面中多有和链接有关的标签

```JavaScript
Content-Security-Policy: default-src 'self'; img-src *; media-src media1.com media2.com; script-src userscripts.example.com
// 图片可以从任何地方加载(注意 "*" 通配符)。
// 多媒体文件仅允许从 media1.com 和 media2.com 加载(不允许从这些站点的子域名)。
// 可运行脚本仅允许来自于userscripts.example.com。

Content-Security-Policy: default-src 'self'
// 一个网站管理者想要所有内容均来自站点的同一个源 (不包括其子域名)


Content-Security-Policy: "default-src 'self'; report-uro /report"
// 请求资源越权上报

Content-Security-Policy: "default-src 'self';form-action 'self'; report-uro /report"
// 限制表单提交的范围
```

#### `HTTP2`

* 谷歌浏览器的并发数是6
* 多路复用:  同一个`tcp`
* 二进制分帧:
* 头部压缩:
* 服务端推送: 

#mydiary/http