# vim一些简单实用
#### 开启编辑模式
* `a:  append`
* `i: insert`
* `o:  open a line beflow`
* `A: append after a line`  
* `I: insert  before a line` 
* `O: append a line above` 
#### vim的一些模式
* 默认的进入`vim`是`normal`模式
* 插入模式: 点击`i|o|a`. 按`esc`回到`normal`模式
* 命令模式: `normal`模式下输入`:`之后执行命令
	* 比如`:w 保存.  退出:q`
	* 比如分屏`:vs (vertical split), :sp(split)`
	* 比如使用`:%s/foo/bar/g`全局替换
#mydiary/vim