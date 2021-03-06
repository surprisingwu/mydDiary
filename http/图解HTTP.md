# 图解HTTP
#### `http`
* 超文本传输协议
#### `www`的构建技术
* 作为页面的文本标记语言的`HTML`(超文本标记语言)
* 作为文档传递协议的`HTTP`
* 指定文档所在地址的`URL`
#### `TCP/IP`协议族
> 互联网相关的协议集合起来总称为`TCP/IP`  
* 协议分层:
	* 应用层, 传输层,网络层和数据链路层
* 应用层:  决定了向用户提供应用服务时通信的活动
	* 协议族内预存了各类通用的应用服务.比如`FTP(文件传输协议),DNS(域名系统),HTTP(协议也处于该层)`
* 传输层:  传输层对上层应用层,提供处于网络连接中的俩台计算机之间的数据传输
	* 传输层有俩个性质不同的协议: `TCP(传输控制协议)和UDP(用户数据报协议)`
* 网络层:  (`IP`协议)有名称网络互联层.
	*  网络层用来处理网络上流动的数据包. 数据包是网络传输的最小数据单位.该层规定了通过怎眼的路径(所谓的传输路线)到达对方计算机,并把数据包传输给对方
* 链路层: (有名数据链路层,网路接口层)
	* 用来处理连接网络的硬件部分.包括操作系统,硬件的设备驱动,`NIC(网络适配器,有名网卡)`,及光纤等物理课件部分(还包括连接器等一切传输媒介). 硬件上的范畴均在链路层的作用范围.
![](%E5%9B%BE%E8%A7%A3HTTP/%E7%BD%91%E7%BB%9C%E5%8D%8F%E8%AE%AE%E5%88%86%E5%B1%82.png)

####  `TCP/IP`通信传输流
* 利用协议族进行网络通信时,会通过分层顺序与对方进行通信. 发送端从应用层往下走,接收端则往应用层往上走
####  与`HTTP`关系密切的协议: `IP,TCP和DNS`
* `负责传输的IP协议`:
	* `IP`协议的作用是把各种数据包传输给对方.(要保证确实传输到对方需要满足各类条件,其中俩个重要的是`IP`地址和`MAC`地址(`Media Access Control Address`)) 
		* `IP`地址: 指明了节点被分配的地址
		* `MAC`地址: 指网卡所属的固定地址
		* `IP`地址和`MAC`地址进行配对. `IP`地址可以变换,但`MAC`地址基本上不会更改
* `确保可靠的TCP协议`:  该协议位于传输层, 提供可靠的字节流服务
	* 字节流服务: 为了方便传输, 将大块数据分割成以报文段为单位的数据包进行管理
	* 确保可靠: `TCP`协议采用了三次握手策略. 握手过程中使用了`TCP`的标志(`flag`)-`SYN(synchronize)和ACK(acknowledgement)`
		* 发送端首先发送一个带`SYN`标志的数据包给对方. 接收端收到后, 回传一个带有`SYN/ACK`标志的数据包以示传达确认信息.最后, 发送端在回传一个带`ACK`标志的数据包,代表握手结束. 
		* 若在握手过程中某个阶段莫名中断,`TCP`协议会再次以相同的顺序发送相同的数据包
![](%E5%9B%BE%E8%A7%A3HTTP/tcp%E4%B8%89%E6%AC%A1%E6%8F%A1%E6%89%8B.png)

* 负责域名解析的`DNS`服务:  位于应用层的协议,它提供域名到`IP`地址之间的解析服务
* 各种协议与`HTTP`协议的关系
	* 在浏览器器输入`url`的时候
	* `DNS`进行域名解析,解析出域名对应的`IP`地址. 
	* `HTTP`协议生成针对`Web`服务器的`HTTP`请求报文.
	* `TCP`协议的职责,为了方便通信,将`http`请求报文分割成报文段
	* `IP`协议的职责: 搜索对方的地址,以便中转一边传送

####  `URL和URI`

* `URI`: 统一资源标识符,  是有某个协议方案表示的资源的定位标识符.协议方案是指访问资源所使用的协议类型名称.包括`URL和URN`
	* 协议方案有30种左右,比如`http,ftp,file,telnet,file,mailto...`	  

```javascript
 	ftp://ftp.is.co.za/rfc/rfc1808.txt
 	http://www.ietf.org/rfc/rfc2396.txt
```

* `URL`: 统一资源定位标识符

* `URI`格式: `http://user:padd@www.example.jp:80/dir/index.html?uid=1#ch1`

	* 协议方案名-登录信息认证-服务器地址-服务器端口号-带层次的文件路径-查询字符串-片段标识符
	* 协议方案名: 使用`http或https`等协议方案名获取访问资源时要指定协议类型
	* 登录信息(认证): 指定用户名和密码作为从服务器端获取资源时必要的登录信息(身份认证)
	* 服务器地址: 使用绝对`URI`必须指定待访问的服务器地址(可以是域名,`IP`地址等)
	* 服务器端口号: 指定服务器连接的网络端口号,不设置,则使用默认的端口号
	* 带层次的文件路径: 指定服务器上的文件路径来定位特指的资源
	* 查询字符串: 针对已指定的文件路径内的资源,可以使用查询字符串传入任意参数
	* 片段标识符: 使用片段标识符通常可标记出已获取资源中的子资源(文档的某个位置)
	* 
	* 
####    简单的`HTTP`协议

* `HTTP`协议用于客户端和服务器端之间的通信
	* 客户端: 请求访问文本或图像等资源的一端称为客户端
	* 服务器端: 提供资源响应的一端称为服务器端
	* 使用`HTTP`协议时,必定是一端担任客户端角色,另一端担任服务器端角色
* 通过请求和响应的交换达成通信
	* 请求必定有客户端发出,而服务器端回复响应
* 请求报文: 
	* 请求行: `method URI http-version`组成
	* 请求头: 可选的请求首部字段组成
	* 请求体: 内容实体

```javascript
  POST /form/entry HTTP/1.1
  Host: test.jp
  Connection: keep-alive
  Content-Type: application/x-www-form-urlencoded
  Conrent-Length: 16
  
  name=ueno&age=37
```

* 响应报文: 
	* 状态行: `http-version status-code  phrases(状态码的原因短语)`
	* 响应头:  可选的首部字段
	* 主体:  返回的内容实体

```javascript
  HTTP/1.1 200 OK
  Date: Tue, 10 Jul 2012 06:50:15 GMT
  Content-Length: 362
  Content-Type: text/html
  
  <html>
      ...
```

* `HTTP`是不保存状态的协议(无状态协议)
	* 好处: 可以更快的处理大量事务,确保协议的可伸缩性
	* 坏处: 用户访问同一个网站的其他资源的时候,需要保存状态. 引入`Cookie`
* 请求`URI`定位资源
	* 通过`URI`可以找到我们需要的资源, 再请求报文中也会体现
* 告知服务器意图的`HTTP`方法
	* `GET`: 获取资源.用来请求访问已被`URI`识别的资源
	* `POST`: 传输实体主体.
	* `PUT`: 传输文件
		* 鉴于`HTTP/1.1`的`PUT`方法自身不带验证机制,任何人都可以上传文件,存在安全性问题,因此一般的`Web`网站不采用该方法. 若配合`Web`应用程序的验证机制,获采用架构设计采用`REST`标准的同类`WEb`网站,就会使用该方法
	* `HEAD`:  获得报文首部
		* 用于确认`URI`的有效性及资源更新的日期时间等
	* `DELETE`: 删除文件. 与`PUT`方法相反. 由于和`PUT`方法一样,请求不带验证,因此用的也比较少
	* `OPTIONS`:  询问支持的方法
			* `OPTIONS`方法用来查询针对请求`URI`指定的资源支持的方法
	* `CONNECT`: 要求用隧道协议连接代理
		* `CONNECT`方法要求在与代理服务器通信时建立隧道,实现用隧道协议进行`TCP`通信. 主要使用`SSL(安全套接层)和TLS(传输层安全)`协议把通信内容加密后经网络隧道传输
	* 持久连接节省通信量

		* 发送`http`请求前要经过`TCP`三次握手, 而断开连接还要经过`TCP`四次挥手. 如果每次请求都这样的话,开销比较大
				* 
![](%E5%9B%BE%E8%A7%A3HTTP/tcp%E8%BF%9E%E6%8E%A5%E5%92%8C%E6%96%AD%E5%BC%80.png)

* 持久连接
	* 持久连接的特点: 只要任意一端没有明确提出断开连接,则保持`TCP`连接状态,也即是建立一次`TCP`连接后进行多次请求和响应的交互
	* 持久连接的好处在于减少了`TCP`连接的重复建立和断开所造成的额外开销,减轻了服务器的负载. 另外, 减少开销的那部分时间,使`HTTP`请求和响应能够更早地结束,这样一定的时间内能够发送更多的请求
* 管线化: 持久连接使得多数请求以管线化方式发送称为可能
![](%E5%9B%BE%E8%A7%A3HTTP/%E6%8C%81%E4%B9%85%E8%BF%9E%E6%8E%A5.png)

*  使用`Cookie`的状态管理
	* 无状态协议优点: 由于不必保存状态,可以减少服务器的`CPU`及内存资源的消耗.
	* `Cookie`: 通过在请求和响应报文中写入`Cookie`信息来控制客户端的状态
		* 服务端通通过`Set-Cookie`的首部字段,告知客户端保存`Cookie`. 下次客户端往该服务器发送请求时,会自动携带该`Cookie`信息

#### `HTTP`报文内容的`HTTP`信息
* `HTTP`报文大致可分为报文首部和报文主体俩块.
* 请求报文和响应报文的结构
	* 报文首部, 报文主体
		* 报文首部: 
			* 请求|状态 行
				* 请求行: 包含用于请求的方法,请求`URI`和`HTTP`版本
						* 状态行: 包含表明响应结果的状态码, 原因短语和`HTTP`版本
			* 请求|响应   首部字段
				* 通用首部字段
				* 实体首部字段
				* 其它.可能包含`HTTP`的`RFC`里未定义的首部(`Cookie`等)
* 编码提升传输效率
* 数据传输时编码,可以有效的处理大量的访问请求. 但是编码的操作需要计算机来完成,因此会消耗更多的`CPU`等资源
* 报文主体和实体主体的差异
	* 实体: 作为请求或响应的有效载荷数据(补充项)被传输,其内容由实体首部和实体主体组成
	* `HTTP`报文的主体: 用于传输请求或响应的实体主体
	* 通常两者是相同的. 但是如果数据在传输的过程中进行编码,两者会发生差异
* 压缩传输的内容编码
	* 常用的内容编码有以下几种:
		* `gzip`
		* `compress`
		* `deflate`
		* `identify`

	* 分割发送的分块传输编码
		* 这种把主体分块的功能称为分块传输编码(`Chunked Transfer Coding`)
		* 分块传输编码的主体会由接收的客户端负责解码,恢复到编码前的实体主体
* 发送多种数据的多部分对象集合
* 获取部分内容的范围请求
	* 指定范围发送的请求叫做范围请求
	* 比如对一份10000 字节大小的资源, 如果使用范围请求,可以只请求5001 ~ 10 000字节内的资源
* 内容协商返回最适合的内容
	* 内容协商机制是指客户端和服务端就响应的资源进行交涉,然后提供给客户端最为适合的资源.内容协商会以响应资源的语言,字符集,编码方式作为判断的基准
	* 请求报文中的某些首部字段就是判断的基准
			* `Accept`
			* `Accept-Charset`
			* `Accept-Encoding`
			* `Accept-Language`
			* `Content-Language`
	* 内容协商技术有以下三种类型:
		* 服务器驱动协商
			* 有服务器端进行内容协商.以请求的首部为参考,再服务器端自动处理
		* 客户端驱动协商
			* 由客户端进行内容协商的方式.用户从浏览器显示的可选乡列表中手动选择
		* 透明协商
			* 是上面俩种协商的结合体, 是由服务器端和客户端各自进行内容协商的一种方法

#### 返回结果的`HTTP`状态码
* 状态码告知从服务器端返回的请求结果
	* 状态码 的职责是当客户端向服务端发送请求时,描述返回的请求结果
	* 状态码的类别: 
![](%E5%9B%BE%E8%A7%A3HTTP/%E7%8A%B6%E6%80%81%E7%A0%81%E7%9A%84%E7%B1%BB%E5%88%AB.png)

* 2XX成功: 响应的结果表明请求被正常处理了
	* 200 ok
		* 在响应报文内, 随状态码一起返回的信息会因为方法的不同而发生改变
	* 204  `No Content`: 请求处理成功,但没有资源可返回
		* 一般在只需要从客户端往服务器发送消息,而对客户端不需要发送新消息内容的情况下使用
	* 206 `Partial Content`: 范围请求
* 3XX 重定向: 表明浏览器需要执行某些特殊的处理以正确处理请求
	* 301 `Moved Permanently`: 永久性重定向
	* 302 `Found` : 临时重定向
	* 303 `See Other`:  该状态码表示请求对应的而资源存在着另一个`URI`,应使用`GET`方法定向获取请求的资源
		* 当上述的三个状态码返回时,几乎所有的浏览器都会把`POST`改成`GET`,并删除请求报文的主体,之后请求会自动再次发送
	* 304 `Not Modified`: 资源已找到, 但未符合条件请求
		* 附带的 条件请求是指采用`GET`方法的请求报文中包含`If-Match, If-Modified-Since, If-None-Match, If-Range, If-Unmodified-Since`中任一首部
	* 307 `Temporary Redirect`: 临时重定向
		* 该状态码会遵照浏览器标准,不会从`POST`变成`GET`
* 4XX  客户端错误
	* 400 `Bad Request`:  该状态码表示请求报文中存在语法错误.
	* 401 `Unauthorized`:  表示发送的请求需要有通过`HTTP`认证(`BASIC 认证, DIGEST认证`)的认证信息
	* 403 `Forbidden` : 请求的资源的访问被服务器拒绝了.(未获得文件系统的访问授权,访问权限出现某些问题等)
	* 404 `Not Found` : 服务器上无法找到请求的资源
* 5XX 服务器错误
	* 500 `Internal Server Error`: 表明服务器端在执行请求时发生了错误
	* 503 `Service Unavailable`:  表明服务器暂时处于超负载或正在进行停机维护

#### 与`HTTP`协作的`Web`服务器

* 用单台虚拟主机实现多个域名
	* 在相同的`IP`地址下,由于虚拟主机可以寄存多个不同主机名和域名的`Web`网站,因此在发送`HTTP`请求时,必须在`Host`首部内完整指定主机名或域名的`URI`
* 通信数据转发程序: 代理,网关, 隧道
	* 代理:  
		* 使用代理服务器的理由: 利用缓存技术减少网络带宽的流量,组织内部针对特定网站的访问控制,以获取访问日志为主要母的,等等
	* 代理按俩种基准分类: 一种是是否使用缓存, 另一种是是否会修改报文
		* 缓存代理:   代理转发响应时,缓存代理会预先先将资源的副本保存在代理服务器上
			* 当代理再次接收到对相同资源的请求时,就可以将之前缓存的资源直接返回
		* 透明代理:
			* 转发请求或响应时,不对报文做任何加工的代理类型被称为透明代理. 反之,称为非透明代理
	* 网关: 
		* 网关的工作机制和代理十分相似. 而网关能使通信线路上的服务器提供非`HTTP`协议服务

![](%E5%9B%BE%E8%A7%A3HTTP/%E7%BD%91%E5%85%B3%E8%BD%AC%E5%8F%91%E8%AF%B7%E6%B1%82.png)

	* 隧道: 
		* 隧道的目的是确保客户端与服务器进行安全的通信
![](%E5%9B%BE%E8%A7%A3HTTP/%E9%9A%A7%E9%81%93%E8%BD%AC%E5%8F%91%E8%AF%B7%E6%B1%82.png)

* 保护资源的缓存
	* 缓存是指代理服务器或客户端本地磁盘内保存的资源副本. 利用缓存可以减少对源服务器的访问.
		* 源服务器: 持有实体资源的服务器
	* 缓存服务器是代理服务器的一种,并归类在缓存代理类型中.
	* 缓存的有效期限
	* 客户端的缓存:  浏览器缓存如果有效,就不会再向服务器请求相同的资源了,可以直接从本地磁盘内读取
![](%E5%9B%BE%E8%A7%A3HTTP/%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%BC%93%E5%AD%98.png)

#### `HTTP`首部

* `HTTP`报文首部 : 主要包括请求行(状态行) 和首部字段
![](%E5%9B%BE%E8%A7%A3HTTP/%E6%8A%A5%E6%96%87%E9%A6%96%E9%83%A8.png)

* `HTTP`首部字段
	* `HTTP`首部字段传递重要信息
	* `HTTP`首部字段结构
		* `首部字段名: 字段值`
		* 报文首部字段重复,根据浏览器内部逻辑的不同,结果可能不一致
	* 4种`HTTP`首部字段类型
		* 通用首部字段
			* 请求和相应报文两方都会使用的首部
			
![](%E5%9B%BE%E8%A7%A3HTTP/%E9%80%9A%E7%94%A8%E9%A6%96%E9%83%A8%E5%AD%97%E6%AE%B5.png)

		* 请求首部字段
			* 从客户端向服务器端发送请求报文时使用的首部

![](%E5%9B%BE%E8%A7%A3HTTP/%E8%AF%B7%E6%B1%82%E9%A6%96%E9%83%A8%E5%AD%97%E6%AE%B5.png)

		* 响应首部字段
			* 从服务器端向客户端返回响应报文时使用的首部

![](%E5%9B%BE%E8%A7%A3HTTP/%E5%93%8D%E5%BA%94%E9%A6%96%E9%83%A8%E5%AD%97%E6%AE%B5.png)

		* 实体首部字段
			* 针对请求报文和相应报文的实体部分使用的首部

![](%E5%9B%BE%E8%A7%A3HTTP/%E5%AE%9E%E4%BD%93%E9%A6%96%E9%83%A8%E5%AD%97%E6%AE%B5.png)

	* 非`HTTP/1.1`首部字段
		* `http`协议中的字段,除了上面的,还有很多使用次数比较多的首部字段如`Cookie, Set-Cookie和Content-DIsposition`等
	* `End-to-end`首部和`Hop-by-hop`首部
		* `http`首部字段将定义成缓存代理和非缓存代理的行为,分成2中类型
			* 端到端首部
			* 逐跳首部
			* 除了下面的逐跳首部字段, 其它都是端到端首部
				* `Connection, Keep-Alive, Proxy-Authernticate,Proxy-Authorization, Trailer, TE, Transfer-Encoding, Upgrage`  

* `HTTP/1.1` 通用首部字段
	* `Cache-Control`: 主要通过一些指令,来控制缓存的机制. 指令可以用于请求和响应时. 指令的参数时可选的,多个指令之间通过`,`分割.
	
```JavaScript
Cache-Control: private, max-age=0, no-cache
```

	* 缓存请求指令
	
![](%E5%9B%BE%E8%A7%A3HTTP/E64D3BFC-C979-4107-BA8E-AAD3A7523388.png)

	* 缓存响应指令
![](%E5%9B%BE%E8%A7%A3HTTP/1D4B73B9-5AD6-4DD1-8619-BDC77AEA93A2.png)

* 表示是否缓存的指令
	* `public`:   明确表明其他用户也可利用缓存
	* `private`:  缓存服务器只对特定用户提供资源缓存的服务
	* `no-cache`:  是够能够使用缓存需要请求到服务器验证下
	* `no-store`: 不使用缓存
	* `s-maxage`:  该指令只适用于供多位用户使用的公共缓存服务器(代理)
		* 当使用该指令时, 则直接忽略对`Expires`首部字段及`max-age`指令的处理
	* `max-age`:   过期时间
		* `Cache-Control: max-age=7*24*60*60`
		* 再`http/1.1`版本,同时存在`Expires和max-age`时, 会优先处理`max-age`
	* `max-stale`: 
		* 如果指令中指定了具体的数值,那么即使过期, 只要仍处于`max-stale`指定的时间内,仍旧会被客户端接收
	* 其他指令...
* `Connection`: 
	* 控制不再转发给代理的首部字段
	* 管理持久连接
* `Date`:  表明创建`HTTP`报文的日期和时间
* `Pragma`: 是`HTTP`之前版本的历史遗留字段

```javascript
// 如果都是以http/1.1版本使用,cache-control最为理想

Cache-Control: no-cache
Pragma: no-cache
```

* `http`首部字段比较多,其它自己在看


*  请求首部字段
	* `Accept`:  该首部字段可告知服务器,用户代理能够处理的媒体类型及媒体类型的相对优先级
		* 文本文件: 
			* `text/html, text/plain, text/css`
			* `application/xhtml-xml, application/xml...`
		* 图片文件
			* `image/jpeg, image/gif, image/png...`
		* 视频文件
			* `video/mpeg, video/quicktime...`
		* 应用程序使用的二进制文件
			* `application/octet-stream, application/zip...`
		* 可以通过`q=0~1`来指定接收的文件类型的权重. 并用分号`;`进行分割
```JavaScript
text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
```

	* `Accept-Charset` : 可用来通知服务器用户代理支持的字符集及字符集的相关优先顺序
```JavaScript
	Accept-Charset: iso-8859-5, unicode-1-1; q=0.8
```

	* `Accept-Encoding`: 告知服务器用户代理支持的内容编码及内容编码的优先级顺序
		* 编码的方式有`gzip, compress, deflate, br等`
```JavaScript
Accept-Encoding: gzip, deflate, br
```

	* `Accept-Language`: 告知服务器用户代理能够处理的自然语言集(指中文或英文等),以及优先级
```JavaScript
Accept-Language: zh-cn,zh; q=0.7,en-us,en;q=0.3
```

	* `Authorization`: 告知服务器,用户代理的认证信息
	* `Expect`:  告知服务器,期望出现的某种特定行文
	* `From`: 告知服务器使用用户代理的用户的电子邮件地址
	* `Host`: 告知服务器,请求的资源所处的互联网主机名和端口号
	
```JavaScript
Host: www.baidu.com
```

	* `If-Match`:  告知服务器匹配资源所用的实体标记`ETag`值, 服务器会比对`If-Match`的字段值和资源的`ETag`值,仅当两者一致时,才会执行请求
		* 形如`If-XXX`这种样式的请求首部字段,都可称为条件请求
![](%E5%9B%BE%E8%A7%A3HTTP/1BB45BAB-C4EB-4DA3-98F5-0BB3693D45DA.png)

	* `If-Modified-Since`:  用于确认代理或客户端拥有的本地资源的有效性
		* 告知服务器若该首部字段的值早于资源的更新时间,则希望能处理该请求. 而在该字段的值后,请求的资源没有更新,则返回状态码`304 Not Modified`的响应
```JavaScript
	Last-Modified: new Date.toString()
```

	* `If-None-Match`:  告知服务器该首部字段的值与资源`Etag`的值不一致时,处理请求
		* `Etag`: 该首部字段的值一般是根据文件的内容生成的值, 文件内容改变时, 值发生改变
	* `If-Range`: 该字段的值若是跟`Etag`值或更新的日期时间匹配一致,那么就作为范围请求处理
	* `If-Unmodified-Since`: 该首部字段和`If-Modified-Since`的作用相反. 它的作用是告知服务器,指定的请求资源只有在字段值内指定的日期时间之后,未发生更新的情况下,才能处理请求.
	* `Max-Forwards`:  最大的转发数, 每次转发该字段的值减一,并重新赋值
	* `Proxy-Authorization`:  告知代理服务器的认证信息. 非代理服务器使用首部字段`Authorization`
	* `Range`: 对于只需获取部分资源的范围请求,包含首部字段`Range`即可告知服务器资源的指定范围
```JavaScript
Range: bytes= 5001 - 10000
```
	* `Refere`: 告知服务器请求的原始资源的`URI`
	* `TE`: 告知服务器客户端能够处理响应的传输编码方式及相对优先级.和`Accept-Encoding`的功能很像,但是用于传输编码
	* `User-Agent`: 会将创建请求的浏览器和用户代理名称等信息传达给服务器
* 响应首部字段
	* `Accept-Ranges`: 告知服务器是否能够处理范围请求,已制定获取服务器某个部分的资源

```JavaScript
	Accept-Ranges: bytes  // 可处理范围请求
	Accept-Ranges: none   // 不可处理
```

	* `Age`: 告知客户端,原服务器在多久前创建了响应. 字段值的单位为妙
	* `ETag`:  告知客户端实体标识. 他是一种可将资源以字符串形式做唯一性标识的方式.服务器会为每份资源分配对应的`ETag`值. 资源更新时,该值也会改变吗
```JavaScript
ETag: 'usagi-1234'   //强ETag值,不论实体发生多么细微的变化都会改变其值
ETag: w/'usagi-1234' // 只有资源发生根本的改变,产生差异才会改变其值
```

	* `Location`: 该字段可以将相应接收方引导至某个与请求`URI`位置不同的资源, 多配合`3XX:Redirection`的响应,提供重定向的`URI`
	* `Proxy-Authenticate`: 会把代理服务器所需求的认证信息发送给客户端
	* `Retry-After`: 告知客户端应该在多久之后再次发送请求. 主要配合`503  Service Unavailable响应`或`3XX Redirect响应一起使用`
	* `Sever`: 告知客户端当前服务器上安装的`HTTP`服务器应用程序信息
	* `Vary`:可对缓存进行控制.源服务器会向代理服务器传达关于本地缓存使用方法的命令
	* `WWW-Authenticate`: 告知客户端适用于访问请求`URI`所指定资源的认证方案(`Basic或是Digest`)和带参数提示的质询.状态码`401 Unauthorized`响应中,肯定带有首部字段`WWW-Authenticate`
* 实体首部字段: 实体首部是包含在请求报文和响应报文中的实体部分所使用的首部,用于补充内容的更新时间等与实体相关的信息
	*  `Allow`: 告知客户端能够支持`Request-URI`指定资源的所有`HTTP`方法
		* `Allow: Get, HEAD`
		* 当服务器接收到不支持的`HTTP`方法时, 会以状态码`405 Method Not Allowed`作为响应返回
	* `Content-Encoding`: 告知客户端服务器对实体的主体部分选用的内容编码方式
		* `Content-Encoding: gzip,br`
	* `Content-Language`: 告知客户端,实体主体使用的自然语言
		* `Content-Language: zh-CN`
	* `Content-Length`: 该字段表明了实体主体部分的大小(单位字节)
		* `Content-Length: 150000`
	* `Content-Location:` 该字段给出与报文主体部分相对应的`URI`
	* `Content-MD5`: 该字段时一串有`MD5`算法生成的值,其目的在于检查主体在传输过程中是否保持完整,以及确认传输到达
	* `Content-Range`: 针对范围请求使用的首部字段
		* `Content-Range: bytes 5001-10000/10000`
	* `Content-Type`: 该字段说明实体主体内对象的媒体类型
		* `Content-Type: text/html:charset=UTF-8`
	* `Expires: `该字段会将资源失效的日期告知客户端. 但是放首部字段`Cache-Control`有指定`max-age`指令时, 比起首部字段`Expires`, 会优先处理`max-age`指令
	* `Last-Modefied`: 指明资源最终修改的时间
* 为`Cookie`服务的首部字段

![](%E5%9B%BE%E8%A7%A3HTTP/E38199B0-F2BB-4286-B286-5FC9CD2728F6.png)
	* `Set-Cookie`: 服务端响应的首部字段,其属性值有
		* `Set-Cookie: status=enable;expires=Tue, 05 Jul 2011 07:26:31`
![](%E5%9B%BE%E8%A7%A3HTTP/A42B6D68-1BA6-4123-BDBA-82333ACE73B4.png)
		* `expires`: 指定浏览器可发送`Cookies`的有效期. 当省略改字段时,其有效期仅限于维持浏览器会话时间段内(通常限于浏览器应用程序被关闭之前)
		* `path`: 该属性可用于限制指定`Cookie`的发送范围的文件目录,该限制可以绕开
		* `domain`: 该属性指定的域名可做到与结尾匹配一致
		* `secure`:  该属性限制`Web`页面仅在`HTTPS`安全连接时,才可以发送`Cookie`
			* `Set-Cookie: name=value;secure`
		* `HttpOnly`:  该属性使`JavaScript`脚本无法获得`Cookie`
			* `Set-Cookie: name=value; HttpOnly`
	* `Cookie`:  首部字段`Cookie`会告知服务器,当客户端想获得`HTTP`状态管理支持时,就会在请求中包含从服务器接收到的`Cookie`
* 其它首部字段
	* `X-Frame-Options`:  该首部字段,用于控制网站内容在其他`Web`网站的`Frame`标签内的显示问题.
		* `DENY`: 拒绝
		* `SAMEORIGIN`:  仅同源域下的页面匹配时许可
	* `X-XSS-Protection`:   用于控制浏览器`XSS`防护机制的开关
		* 0: 将`XSS`过滤设置成无效状态
		* 1:  将`XSS`过滤设置成有效状态
	* `DNT`:  拒绝个人信息被采集,是表示拒绝被精准广告追踪的一种方法
		* 0 : 同意被追踪
		* 1:  拒绝被追踪

#### 确保`Web`安全的`HTTPS`
* `HTTP`的缺点
	* 通信使用明文(不加密), 内容可能会被窃听
		* 通信的加密. `HTTP`协议中没有加密机制,但可以通过和`SSL(安全套接层)或TLS(安全层传输协议)`的组合使用,加密`HTTP`的通信内容
		* 与`SSL`组合使用的`HTTP`被称为`HTTPS(超文本传输安全协议)或 HTTP over SSL`
		* 对传输内容(报文主体)的加密,有可能被篡改
	* 不验证通信方的身份, 因此有可能遭遇伪装
		* 任何人都可发起请求
		* 查明对手的证书
	* 无法证明报文的完整性, 所以有可能以遭篡改
		* 接收到的内容可能有误
* `HTTP`+加密+认证+完整性保护 = `HTTPS`
	* `HTTP`加上加密处理和认证以及完整性保护即是`HTTPS`
	* `HTTPS`是身披`SSL`外壳的`HTTP`
![](%E5%9B%BE%E8%A7%A3HTTP/754FC27F-5A2B-4393-AFC0-476DD0FC2BA8.png)

	* 相互交换密钥的公开密钥加密技术
		* 使用俩把密钥的公开密钥解密
		* `https`采用混合加密机制.`https`采用共享密钥加密和公开密钥加密两者并用的缓和加密
	* 证明公开密钥正确性的证书
		* 可以使用有数字证书认证机构(`CA`)和其相关机关颁发的公开密钥证书(数字证书或直接成为证书)
			* 接到证书的客户端可以使用证书认证机构的公开密钥,对证书上面的签名进行验证
		* 可证明组织真实性的`EV SSL`证书
		* 由自认证机构颁发的证书称为自签名证书
			* 独立构建的认证机构叫做自认证机构,由自认证机构颁发的’无用’证书也被戏称为自签名证书
	* `HTTPS`的安全通信机制
		* `SSl`慢: 一直是指通信慢. 另一种是指由于大量消耗`CPU`及内存等资源,导致处理速度变慢
#### 确认访问用户身份的认证
* `HTTP`: 使用认证的几种方式: 
	* `BASIC`:  基本认证
		* 该认证不够灵活,且达不到多数`Web`网站期望的安全性等级
		
```JavaScript
res.writeHead(401, {
      'WWW-Authenticate': 'Basic realm = "Input Your Id and Password"'
    })
```
	* `DIGEST`: 摘要认证. 使用质询/响应的方式
		* 质询响应方式是指,一开始一方会先发送认证要求给另一方,接着使用从另一方那接收到的质询码计算生成响应码并返回的认证方式
		* 该认证和`BASIC`认证一样,使用不灵活且安全等级不高
	* `SSL`; 客户端认证
		* `SSL`客户端认证采用双因素认证
		* 需要一定的费用
	* `FormBase`: 基于表单认证
* 认证多半基于表单认证
* `Session管理`及`Cookie`应用
	* 加盐(给 密码加盐): 由服务器随机生成的一个字符串,但是要保证长度足够长,并且真正随机生成的. 然后把它和密码字符串相连接生成散列值.
	* `Session:`  是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；
	* `Cookie`: 是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现`Session`的一种方式。
	* 不要混淆 `session` 和 `session `实现。
		* 本来 `session `是一个抽象概念，开发者为了实现中断和继续等操作，将 `user agent` 和` server `之间一对一的交互，抽象为“会话”，进而衍生出“会话状态”，也就是 `session` 的概念。 而 `cookie` 是一个实际存在的东西，`http` 协议中定义在 `header` 中的字段。可以认为是 `session` 的一种后端无状态实现。而我们今天常说的 “session”，是为了绕开 cookie 的各种限制，通常借助 cookie 本身和后端存储实现的，一种更高级的会话状态实现。所以 cookie 和 session，你可以认为是同一层次的概念，也可以认为是不同层次的概念。具体到实现，session 因为 session id 的存在，通常要借助 cookie 实现，但这并非必要，只能说是通用性较好的一种实现方案。

#### 基于`HTTP`的功能追加协议
* `SPDY`的设计与功能
	* 多路复用流
		* 通过单一的`TCP`连接.可以无限制处理多个`HTTP`请求
	* 赋予请求优先级
		* 可以给请求逐个分配优先级顺序
	* 压缩`HTTP`首部
		* 压缩`HTTP`请求和响应的首部
	* 推送功能
		* 支持服务器主动向客户端推送数据的功能
	* 服务器提示功能
		* 服务器可以主动提示客户端请求所需的资源
![](%E5%9B%BE%E8%A7%A3HTTP/0F263B26-7F02-4092-B742-5706894D9731.png)

* `WebSocket协议`: 
	* 推送功能: 
		* 支持服务器想客户端推送数据的推送功能
	* 减少通信量
		* 通信的首部比较小
* 期盼已久的`HTTP/2.0`

#### `Web`的攻击技术

* 针对`Web`的攻击技术
	* 针对`Web`应用的攻击模式
		* 主动攻击
			* 以服务器为目标的主动攻击. 主动攻击是指攻击者通过直接访问`Web`应用,把攻击代码传入的攻击模式.主动攻击模式里具有代表性的攻击是`SQL注入攻击`和`OS`命令注入攻击
		* 被动攻击
			* 以服务器为目标的被动攻击. 被动攻击是指利用圈套策略执行攻击代码的攻击模式. 被动攻击模式中具有代表性的攻击是跨站脚本攻击和跨站点请求伪造.
	* 因输出值转义不完全引发的安全漏洞
		* 实施`Web`应用的安全对策可大致分为以下俩种部分
			* 客户端的验证
			* `Web`应用端(服务器端)的验证
				* 输入值验证
				* 输入值转义
		* 跨站脚本攻击:
			* 跨站脚本攻击`XSS`是指通过存在安全漏洞的`Web`网站注册用户的浏览器内运行非法的`HTML`标签或完成`JavaScript`进行的一种攻击
			* 造成的影响
				* 利用虚假输入表单骗取用户个人信息
				* 利用脚本窃取用户的`Cookie`值,被害者再不知情的情况下,帮助攻击者发送恶意请求
				* 显示伪造的文章或图片
			* `XSS`是攻击者利用预先设置的陷阱触发的被动攻击
		* `SQL`注入攻击
			* 会执行非法`SQL`的`SQL`注入攻击. 该攻击是指针对`Web`应用使用的数据库,通过运行非法的`SQL`而产生的攻击.
			* 造成的影响
				* 非法查看或篡改数据库内的数据
				* 规避认证
				* 执行和数据库服务器业务关联的程序等
		* `OS`命令注入攻击
			* `OS`命令注入攻击是指通过`Web`应用,执行非法的操作系统命令达到攻击的目的. 只要在能调用`Shell`函数的地方就有存在被攻击的风险
		* `HTTP`首部注入攻击
			* `HTTP`首部注入攻击是指攻击者通过在响应首部字段内插入换行,添加任意响应首部或主体的一种攻击. 属于被动攻击模式
				* 向首部主体内添加内容的攻击称为`HTTP`响应截断攻击
			* 造成的影响:
				* 设置任何`Cookie`信息
				* 重定向至任意`URL`
				* 显示任意的主体(`HTTP`响应截断攻击)




#mydiary/http#