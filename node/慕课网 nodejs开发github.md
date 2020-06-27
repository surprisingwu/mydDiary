# 慕课网 nodejs开发github
####  `nodejs和javascript`的区别
* `ECMAScript`: 定义了语法规范,写`javascript和nodejs`都必须遵守
	* 变量定义,循环,判断,函数,原型和原型链,作用域和闭包等
	* 不能操作`DOM`,不能监听click事件,不能发送ajax请求
	* 不能处理http请求,不能操作文件
	* 即只有`ECMAScript`,几乎什么都做不了
* `javascript`: 使用`ECMAScript`语法规范,外加`Web API (W3c)`,缺一不可
	* `DOM`操作,`BOM`操作,事件绑定,`Ajax`等
	* 俩者结合,即可完成浏览器的任何操作
* `nodejs`: 使用`ECMAScript`语法规范,外加`nodejs API`,俩者缺一不可.
	* 除了`httjp请求,处理文件等
	* 俩者结合,即可完成`sever`端的任何操作
	
#### `server `开发和前端开发的区别
* 需要改变思维,切换思路.
* 服务稳定性
	* `server`端可能会遭受各种恶意攻击和误操作
	* 单个客户端可以意外挂掉,但是服务不能
	* `PM2`进程守候
* 考虑内存和`CPU`(优化,扩展)
	* 客户端独占一个浏览器,内存和`CPU`都不是问题
	* `server`端要承载很多请求,`CPU`和内存都是稀缺资源
	* `stream`写日志,使用`redis存session`
* 日志记录
	* 前端也会参与写日志,但只是日志的发起方,不关心后续
	* `erver`端要记录日志,存储日志,分析日志,前端不关心
	* 多种日志记录方式, 根据场景选择
* 安全
	* `server`端要随时准备接收各种恶意攻击,前端则少很多
	* 如:越权操作,数据库攻击等
	* 预防`xss攻击和sql`注入
* 集群和服务拆分
	* 产品发展速度快,流量可能会迅速增加
	* 如何通过扩展机器和服务拆分来承载大流量?
	* 课程设计上支持服务拆分
#### 博客项目介绍
* 目标: 
	* 开发一个博客系统,具有博客的基本功能
	* 只开发`server`端,不关心前端
* 需求:
	* 首页,作者主页,博客详情页,登录页,管理中心,新建页,编辑页
	*  需求一定要明确,需求知道开发
* 技术方案: 
	* 数据如何存储
	* 如何与前端对接,即接口设计
#### http 请求概述
* `DNS`解析,建立`TCP`链接,发送`http`请求
* `server`接收到`http`请求,处理,返回
* 客户端接收到数据,处理数据(如渲染页面,执行`js`)
#### 环境搭建
* `nodemon`: 可以检测文件的变化,自动启动服务
* `cross-env`: 用来设置环境变量,可以跨平台
#### 简单的`sql`语句
* 增加: `insert into tableName(username,`password`,realname) vlaues ('zhangsan', '123', '李四')`
* 查询:
	* 查询全部: `select * from users;`
	* 查询指定的列: `select id,username from users;`
	* 条件查询: `select * from users where username='zhangsan'`
	* 模糊查询: `select * from users where password  like '%zhang%';`
	* 模糊查询并排列: `select * from users where password like '%1%' order by id desc;`
* 删除:
	* 直接删除: `delete from users where username='zhangsan';`
	* 软删除,添加一个状态键, 比如1, 删除时,设为0. 恢复时设置为1
		* `update users set state=0 where username='zhangsan';`
		* `update users set state=1 where username='zhangsan';`
* 更新: `update users set username='zhangsan' where username='zhansan';`

#### `nodejs`链接数据库

* 可以对执行的`sql`语句进行封装, 来对数据库进行增删改查;

```javascript
// 数据库版本8以上,链接会报错. 可以执行下面的语句

ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '456852@wyt';

const mysql = require('mysql');
const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  port: '3306',
  password: '456852@wyt',
  database: 'myblog',
  insecureAuth : true
});

connection.connect()

const query = `insert into users(username,password,realname) values('mazi','12345','麻子')`;
connection.query(query, function (error, results, fields) {
  if (error) {
    console.log(error);
    return
  }
  
  console.log(results);
});

connection.end();
```

#### 登录
* `cookie`
	* 存储在浏览器的一段字符串(最大5kb)
	* 跨域不共享. 同源请求才携带
	* 格式.如 k1=v1;k2=v2;k3=v3;因此可以存储结构化数据
	* 每次放送`http`请求, 会将请求域的`cookie`一起发送给`server`
	* `server`可以修改`cookie`并返回浏览器
	* 浏览器也可以通过`javascript`修改`cookie`(有限制)
* `sesseion`直接用一个变量来存储
	* 目前`session`直接是`js`变量,放在`nodejs`进程内存中
	* 第一,进程内存有限,访问量过大. 内存暴增怎么办?
	* 第二,正式线上运行是多进程,进程之间内存无法共享.
* `redis`:
	* `web server`最常用的缓存数据库,数据存放在内存中
	* 双方都是独立的,都是可扩展的(例如都扩展成集群)
	* 包括`mysql`也是可以扩展
* 为何`session`适合用`redis`
	* `session`访问频繁,对性能要求极高
	* `session`可不考虑断电丢失数据的问题(内存的硬伤)
	* `session`数据量不会太大(相比`mysql`中存储的数据)
* 为何网站数据不适合用`redis`
	* 操作频率不是太高(相比`session`操作)
	* 断电不能丢失,必须保留
	* 数据量太大,内存成本太高

#### `IO`操作的性能瓶颈
* `IO`包括网络`IO`和文件`IO`
* 相比于`CPU`计算和内存读写, `IO`的突出特点就是慢. 引出流的概念
	
![](%E6%85%95%E8%AF%BE%E7%BD%91%20nodejs%E5%BC%80%E5%8F%91github/WechatIMG15161.png)


#### `crontab`日志拆分
* 日志是按行存储的, 一行就是一条日志.可以使用`nodejs`的`readline`来读取.
* 设置定时任务, 格式: `***** command`
* 输入`crontab -e`编辑完后,  报错
	* `sudo vi ~/.vimrc`
	* ` autocmd filetype crontab setlocal nobackup nowritebackup`
* 再输入`crontab -l`,就可以查到设置的任务

#### 安全
* `sql`: 窃取数据库内容
* `xss攻击`: 窃取点断的`cookie`内容
	* 再页面展示内容中掺杂`js`代码, 以获取网页信息
	* 预防措施: 对输入和输出进行转义, 后台对`cookie`设置`httpOnly`.
* 密码加密: 保障用户信息安全(重要)
	* 攻击方式: 获取用户名和密码, 再去尝试登陆其他系统
	* 预防措施: 将密码加密,即便拿到密码后也不知道明文.

#### express
* `express`是`nodejs`最常用的`web server`框架
	* 封装基本的`web api `的工具
	* 有一定的流程和标准
* `express`中间件的原理
	* 通过`app.use`来注册中间件,先收集起来
	* 遇到`http`请求,根据`path`和`method`判断触发那些
	* 实现`next`机制,即上一个通过`next`触发下一个

#### koa

#### pm2
* 核心价值: 进程守护,以及多进程的启动和线上日志的记录.
* `pm2 start xxx`: 启动某一个进程
* `pm2 list`:  查看进度列表
* `pm2 restart <AppName>/<id>`:  重启某一个文件
* `pm2 stop <AppName>/<id>`: 停止某一个进程
* `pm2 delete <appname>/<id>` : 删除某一个进程
* `pm2 info <appname>/<id>`:  查看当前进程的信息
* `pm2 log <AppName>/<id>`:  查看当前日志的打印
* `pm2 monit <AppName>/<id>`:  监控内存的一些信息
* 配置文件: `pm2.conf.json`
	* 修改启动命令,重启
	* 访问`server`,检查日志文件的内容(日志记录是否生效)

```javascript
{
  "apps": {
    "name": "pm2-test-server",
    "script": "app.js",
    "watch": true,
    "ignore_watch": [
      "node_modules",
      "logs"
    ],
    "err_file": "logs/err.log",
    "out_file": "logs/out.log",
    "log_date_format": "YYYY-MM-DD HH:mm:ss"
  }
}

```

* 多进程:
	* 多进程可以充分利用内存和`cpu`
	* 多个进程之间的数据是无法共享的
	* 多进程访问一个`redis`, 实现数据共享.
* 单进程
	* 单个进程的内存是受限的 
	* 无法充分利用多核`cpu`的优势

#### 关于运维
* 服务器运维, 一般都由专业的`OP`人员和部门来处理
* 大公司都有自己的运维团队
* 中小型推荐使用一些云服务, 如阿里云的`node`平台

![](%E6%85%95%E8%AF%BE%E7%BD%91%20nodejs%E5%BC%80%E5%8F%91github/WechatIMG25326.png)



#mydiary/node