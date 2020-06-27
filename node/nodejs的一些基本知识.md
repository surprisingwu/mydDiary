# nodejs的一些基本知识
####  `require`加载`js`的特点
* `module`被加载的时候执行, 加载后缓存
* 一旦出现某个模块被循环加载,就只输出已经执行的部分,未执行部分不会输出

#### 谷歌浏览器调式`nodejs`
* `node --inspect-brk XX.js`, 然后谷歌浏览器里输入`chrome://inspect`
#mydiary/node

#### `PATH`: 相关的问题

* `__dirname, __filename`: 总是返回文件的绝对路径
* `process.cwd()`: 总是返回执行`node`命令所在文件夹
* 在`require`方法中总是相对当前文件所在文件夹
	* 再其他地方和`process.cwd()`一样, 相对`node`启动的文件夹