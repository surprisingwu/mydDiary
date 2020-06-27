# Effective Javascript
#### 使用数组和字典(对象)
* `js对象的核心`: 是一个字符串属性名称与属性值的映射表
* `for … in`:循环, 会遍历对象的原型对象上的属性和方法(可以用`hasOwnProperty(key)`来排除)
* 再使用轻量的对象存储属性和方法时, 尽可能直接继承自`Object`,也可以通过`Object.creat(null|prototypeObj)`避免原型污染和继承自某一个对象
* 数组的遍历最好不要用`for...in`, 可以使用`for`或者数组自身的的遍历方法`forEach  map  filter find findIndex`等, `for...in`遍历是对象的`key`, 而数组对应的`key`为其索引, 但是再用`for…in`循环遍历时返回的是字符串, 因此如果此时用`key`进行计算时可能会得道不正确的答案

* 通过`for`循环迭代数组时, 可以通过变脸保存数组的长度, 减少性能消耗(不保存时, 每次循环都会简单的计算数组的长度, 而且循环的终止条件是简单且确定的)
* 数组的`some和every`迭代方法可以提前跳出循环, 其他的不可以
* 伪数组(arguments或者nodeList)的特性, 有`length`属性, 且`length`的值大于索引的最大值, 这样就可以通过`[].forEach.call(arrLike,fn)`调用数组的方法
* 类数组转数组的方式`[].slice.call(arrLike)`,也可以使用`EC6的api  Array.from(arrLike)`转化为数组
* 创建数组最好使用数组的字面量的形式
#### 库和API的设计

* `EC5`中函数设置默认值时, 使用逻辑运算符`||`是不安全的, 如果函数接手`0或者''`,函数会取默认值而忽视我们传的这些值. 可以通过三元运算符检测该参数是否为`undefined`再设置默认值
* `API`绝不应该重载与其他类型有重叠的类型
* 再设计库或方法时避免使用不必要的状态, 无状态的`API`提供的函数或方法的行为只取决于输入,而与程序的状态改变无关. 有状态的函数或方法造成程序间的耦合度比较高, 不利于维护, 但有时是必须的
* 方法接受一个参数对象时,  并且需要设置默认参数时, 可以不必检测每一个参数, 直接通过`extend` 方法来设置默认参数(如果有值会被覆盖, 没有使用默认值)
* 检测一个对象是否是数组的方法
	* `obj instanceof Array`: 大部分情况下正常, 但是夸`iframe`时将不正常. 当跨`iframe`通信时, 一个`frame`中的数组不会继承自另一个`frame`的`Array.prototype`
	* `Arrar.isArray(obj)`:  没有上面`iframe`的限制, `IE8之前不支持`
	* `Object.prototype.toString.call(obj) === '[object Array]'`: 都支持
* 避免强制转换和重载的混用, 考虑防御性的监视非预期的输入. 设计参数的时候应考虑到可能接收到的参数, 避免参数类型不对引起其他的异常
* 设计库的时候可以考虑是否支持链式调用, 省去反复的获取某一个对象的逻辑, 使代码逻辑更加简单(返回`this`而不是`undefined`, 一般支持链式调用的`api`偏向于设置而不是获取新的对象)

#### 并发
* 不要阻塞`I/O`事件队列. 由于`js`是单线程的, 每次只能处理一个事件,同步操作会阻塞页面的渲染和其他的交互, 是一种极不好的交互
* 异步`API`使用回调函数来延缓处理代价高昂的操作以避免阻塞主应用程序
* `JavaScript`并发地接收事件, 但会使用一个事件队列按序地处理事件处理程序
* 使用嵌套或命名的回调函数按顺序地执行多个异步操作, 避免将可被并行执行的操作顺序化(如果一些操作依赖异步返回的结果, 就要嵌套了, 或使用`EC7`的`async await函数了`)
* 对于异步操作的错误处理, 可以使用一个函数进行封装, 减少代码的重复
* 循环不能是一步的
* 使用递归函数在事件循环的单独轮次中执行迭代, 在事件循环的单独轮次中执行递归, 并不会导致调用栈溢出(尾递归优化)
```javascript
function downloadOneAsync(urls, onsuccess, onfailure) {
	var n = urls.length
	function tryNextURL(i) {
		if(i > n) {
			onfailure('all downloads failed')
			return
		}
		downloadAsync(urls[i], onsuccess, function() {
			tryNextURL(i+1)
		})
	}
	tryNextURL(0)
}
// 注意异步函数里面的值无法return到外面, 只能通过回调进行传值
```

* 避免在主事件队列中中执行代价高昂的算法, 一些循环调用可以优化成递归(尾递归,每一次递归需要异步的调用,这样可以避免大量的计算导致的阻塞页面)
```JavaScript
	// 节选自书中 搜索好友的逻辑, 把大量的通不运算转换为异步的操作

Member.prototype.inNetwork = function(other, callback) {
	var visited = {}
	var worklist = [this]
	
	function next() {
		if(worklist.length === 0) {
			callback(false)
			return	
		}
		var member = worklist.pop()
		// ...
		if(member === other) {
			callback(true)
			return
		}
		// ...
		setTimeout(next, 0)
	}
	
	setTimeout(next, 0)
}
```

* 注意数组更新的时候, 即设置一个索引属性, 总是确保数组的length属性值大于所引

```JavaScript
	
	// Promise.all(promiseArr).then(arr=>{ //...})
	// 可以替代下面的逻辑, 但是了解异步的操作还是比较重要的
	function downloadAllAsync(urls, onsuccess, onerror) {
		var pending = urls.length
		var result = []
		
		if(pending === 0) {
			setTimeout(onsuccess.bind(null, result),0)
			return
		}
		urls.forEach(function(url, i) {
			downloadAsync(url, function(text) {
				if(result) {
					result[i] = text
					pending --
					if(pending === 0) {
						onsuccess(result)
					}
				}
			}, function(error) {
				if(result) {
					result = null
					onerror(error)
				}
			})
		})
	}

```

* 绝不要同步的地调用异步的回调函数

```JavaScript
	var cache = new Dict()

	function downloadCachingAsync(url, onsuccess, onerror) {
		if(cache.has(url)) {
			var cached = cache.get(url)
			// 这里的onsuccess是异步的回调
			setTimeout(onsuccess.bind(null, cached), 0)
			return
		}
		return downloadAsync(url, function(file){
			cache.set(url, file)
			onsuccess(file)
		}, onerror)
	}

// 同步地调用异步的回调函数扰乱了预期的操作序列, 并可能导致意想不到的交错代码
// 使用异步的api,比如setTimeout函数来调度异步回调函数, 使其运行于另一个回合
```


#mydiary/javascript/advance