# `linux`操作文件的一些命令
#### 文件的增删改查
* `mkdir folderName` : 创建一个文件夹
* `touch fileName`: 创建一个文件
* `mv fileName|folderName path`: 将文件/文件夹移动到指定目录下
* `rm -rf fileName|folderName`: 删除文件(夹)
	* `-f`: 强制删除
	* `-r`: 递归删除
* `mv fileName|folderName sm/newName`: 没有后面的移动路径,则只是修改文件(夹)的名称. 有后面的路径, 则是移动到指定路径下并重新命名
* `cp fileName|folderName path`:  复制文件(夹)(到指定的路径下)
	* `cp a.txt ../b.txt`: 将`a.txt`复制到上级目录中,并重新命名为`b.txt`
* `cat fileName`:  查看指定文件的内容
* `head [-n num] fileName`: 查看文件前十行(指定前多少行)
* `tail [-n num] fileName`: 查看文件后10行(指定后多少行)
* `less fileNmae`: 以翻页的形式查看文件的内容(按上下键翻页,按`q`退出)
* `find 查找位置 查找参数`
	* `find . -name *name* `: 再当前位置下查找文件名含`name`字符的文件
	* 查找参数: `-name -perm -user - group -ctime -type -size`

* `pwd`: 返回当前的工作路径
* `ls -s`: 显示文件和文件的大小


#### `curl`工具发起简单的`http`的请求
* `curl -X GET pathStr`: 发起简单的`get`请求
* `curl path`: 发起`get`请求
* `curl -v path`: 显示`get`请求全过程解析
* `curl -I path`: 只显示头部信息
* `curl -i path`: 只显示请求的响应报文
* `curl -b|--cookie 'bar=ar;test=hehe' path`: 设置`cookie`
* `post请求`:
	* `curl -d'name=wyt&age=18' path`

#### 查找进程和杀死进程

* `ps -ef|grep nginx`: 找到`nginx:master`的进程号
	* `kill -QUIT processNum`: 从容的停止
	* `kill -TERM processNum`: 立刻停止
	* `kill -INT processNum`: 立刻停止

#### `Mac`启动关闭`nginx`的命令

* `sudo nginx`: 启动
* `sudo nginx -s stop`: 停止
* `sudo nginx -s reload`: 重新启动
* `sudo  nginx -t`: 检测`nginx.conf`文件
* `brew services start nginx`
* `brew services stop nginx`

#### 查看进程和杀死进程
```javascript
sudo lsof -i :7000

sudo kill -9 pid
```


####  刷新缓存

```js
sudo killall -HUP mDNSResponder

```

#### npm 查询全局安装的包
```js
npm list -g —depth 0
```

	
#mydiary/linux#