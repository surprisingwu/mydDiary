# javascript 深入系列, 以及一些方法的手写

#### 什么是闭包?
> `MDN`对闭包的定义: 闭包是指那些能够访问自由变量的函数.  
* 自由变量: 只在函数中使用的, 但既不是函数参数也不是函数的局部变量的变量.
* 闭包有俩部分组成: 函数和函数能够访问的自由变量
* 从技术的角度上讲,所有的函数都形成了闭包.(函数的`scope`属性指向上一层的上下文,因此可以访问上层上下文的自由变量)
* 从实践的角度上讲
	* 即使创建它的上下文已经销毁, 它仍然存在(比如,内部函数从父函数中返回)
	* 在代码中引用了自由变量

```javascript
const scope = 'global scope'
function checkscope() {
	const scope = 'local scope'
	function f() {
		return scope
	}
	return f
}
const foo = checkscope()
foo()
```

#### javascript 的参数都是按值传递的
* 基本类型的参数是直接按值传递的, 引用类型的参数是赋值的对象的引用地址(指针)

#### 手写`bind和apply,call`

```javascript
 // call 的实现
Function.prototype.call2 = function () {
  const args = Array.prototype.slice.call(arguments)
  const o = args.shift() || window
  o.fn = this

  const result = eval('o.fn('+args+')')
  delete o.fn
  return result
}

 // apply的实现

Function.prototype.apply2 = function (o,arr) {
  o = o || window
  let result;
  o.fn = this
  if(arr) {
    if(Array.isArray(arr)) {
      result = eval('o.fn('+arr+')')
    } else {
      throw new Error('CreateListFromArrayLike called on non-object')
    }
  } else {
    result = o.fn()
  }
  delete o.fn
  return result
}

 // 手写 bind的实现, 考录到了bind返回的函数当做构造函数使用
Function.prototype.bind2 = function (context) {
  const slice = Array.prototype.slice
  if(typeof  this !== 'function') {
    throw  new Error("Function.prototype.bind - what is trying to be bound is not callable")
  }
  const args = slice.call(arguments,1)
  const noop = function () {}
  const self = this
  const fBound =   function () {
    return self.apply(this instanceof fBound?this:context, args.concat(slice.call(arguments)))
  }

  noop.prototype = this.prototype
  fBound.prototype = new noop()

  return fBound
}

 // 手写new

function createInstance() {
  const obj = new Object()
  const Constructor = Array.prototype.shift.call(arguments)
  Object.setPrototypeOf(obj, Constructor.prototype)
  const ret = Constructor.apply(obj, arguments)
  return typeof ret === 'object' ? ret : obj
}


```

#### promise方法的手写基本实现

```javascript
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'
let PROMISE = null
function resolvePromise(p, x, resolve, reject) {
  if (x instanceof MyPromise) {
    x.then(function(value) {
      resolvePromise(p, value, resolve, reject)
    }, reject)
  }
  let called = false
  if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
    try {
      let then = x.then
      if (typeof then === 'function') {
        then.call(
          x,
          y => {
            if (called) return
            called = true
            resolvePromise(p, y, resolve, reject)
          },
          e => {
            if (called) return
            called = true
            reject(e)
          }
        )
      } else {
        resolve(x)
      }
    } catch (e) {
      if (called) return
      called = true
      reject(e)
    }
  } else {
    resolve(x)
  }
}
function MyPromise(executor) {
  this.status = PENDING
  this.value = null
  this.onResolved = []
  this.onRejected = []

  const resolve = value => {
    if (value instanceof MyPromise) {
      return value.then(resolve, reject)
    }
    setTimeout(() => {
      if (this.status === 'pending') {
        this.status = FULFILLED
        this.value = value
        this.onResolved.forEach(fn => fn(this.value))
      }
    }, 0)
  }
  const reject = reason => {
    setTimeout(() => {
      if (this.status === 'pending') {
        this.status = REJECTED
        this.value = reason
        this.onRejected.forEach(fn => fn())
      }
    }, 0)
  }
  try {
    executor(resolve, reject)
  } catch (e) {
    reject(e)
  }
}

MyPromise.prototype.then = function(onFulfilled, onRejected) {
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : e => {
          throw e
        }

  return (PROMISE = new MyPromise((resolve, reject) => {
    if (this.status === PENDING) {
      this.onResolved.push(() => {
        try {
          const ret = onFulfilled(this.value)
          resolvePromise(PENDING, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
      this.onRejected.push(() => {
        try {
          const ret = onRejected(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }
    if (this.status === FULFILLED) {
      setTimeout(() => {
        try {
          const ret = onFulfilled(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }
    if (this.status === REJECTED) {
      setTimeout(() => {
        try {
          const ret = onRejected(this.value)
          resolvePromise(PROMISE, ret, resolve, reject)
        } catch (e) {
          reject(e)
        }
      })
    }
  }))
}
MyPromise.resolve = function (v) {
  return (PROMISE = new MyPromise((resolve, reject) => {
    resolvePromise(PROMISE, v, resolve, reject)
  }))
}

MyPromise.resolve({ then: function (d) { return 188}}).then(d => console.log(d))
 new Promise((resolve, reject) => {
   setTimeout(() => {
     resolve(12)
   }, 0)
 }).then(v => {
   console.log(v);
   return Promise.then(v => console.log('hehe'))
 })


```


#### 防抖的函数实现
* 使用的场景,主要是下面触发频率比较高的场景,尤其是伴随着有网络请求
	* 如搜索
```javascript

function debounce(func,wait,immediate){

	let timeout,result;

	const debounced=function(){
		const context=this;
		const args=arguments;

		if(timeout) clearTimeout(timeout);
		if(immediate ){
		 如果已经执行过，不再执行
			const callNow=!timeout;
			timeout=setTimeout(function(){
				timeout=null;
				func.apply(context, args)
			},wait)
			if(callNow)result=func.apply(context,args)
		} else {
			timeout=setTimeout(function(){
				func.apply(context,args)
			},wait);
		}
		return result;
};

debounced.cancel=function(){
	clearTimeout(timeout);
	timeout=null;
};

return debounced;
}

```

#### 节流的实现

```js
function throttle(func,wait,options){
	var timeout,context,args,result;
	var previous=0;
	if(!options)options={};

	var later=function(){
	previous=options.leading===false ?0:*new*Date().getTime();
		timeout=null;
		func.apply(context,args);
		if(!timeout)context=args=null;
};

	var throttled=function(){
	 	var now=new Date().getTime();
		if(!previous&&options.leading===false)previous=now;
		var remaining=wait-(now-previous);
		context=this;
		args=arguments;
		if(remaining<=0||remaining>wait){
			if(timeout){
			clearTimeout(timeout);
			timeout=null;
		}
		previous=now;
		return func.apply(context,args);
		if(!timeout)context=args=null;
		}elseif(!timeout&&options.trailing!==false){
		timeout=setTimeout(later,remaining);
		}
	};
throttled.cancel=function(){
	clearTimeout(timeout);
	previous=0;
	timeout=null;
}
return throttled;
}

```

#mydiary/javascript/advance#