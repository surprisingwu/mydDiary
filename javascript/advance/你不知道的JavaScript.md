# 你不知道的JavaScript
#### 作用域

* 变量的赋值操作会执行俩个动作, 首先编译器会在当前作用域中声明一个变量(如果之前没有声明过),然后在运行时引擎会在作用域中查找该变量, 如果能够找到就会对它赋值
* `LHS和RHS`查询:  赋值操作的左侧和右侧 
	* `LHS`:  赋值时的查询,试图找到变量的容器本身,从而可以对其赋值
		* `var a = 12`, 赋值操作分为两步
			* 首先,`var a`再其作用域中声明新变量. 这会在最开始的阶段,也就是代码执行前进行
			* 接下来, `a = 2`会查询变量`a`并对其进行赋值
	* `RHS`:  与简单的查找某个变量的值别无二致
* 异常
	* `LHS`:当引擎在执行`LHS`查询时,如果在顶层(全局作用域)中也无法找到目标变量,全局作用域中就会创建一个具有该名称的变量,并将其返还给引擎,前提是程序运行在非**非严格模式下**
	* `RHS`:  查询再所有嵌套的作用域中遍寻不到所需的变量,引擎就会抛出`ReferenceError`异常

```javascript
function foo(a) {
	console.log(a); // 2
}

foo(2)   

// rhs: foo a
// lhs: a = 2
```
#### 词法作用域

> 定义在词法阶段的作用域. 词法作用域是有你在写代码时将变量和块作用域写在哪里决定的, 因此当词法分析器处理代码时会保持作用域不变(词法作用域再变量书写时就已经定了, 与执行环境无关)  

- - - -
* 词法作用域：是一套关于引擎如何寻找变量以及会在何处找到变量的规则。词法作用域最重要的特征是它的定义过程发生在代码的书写阶段
* 词法作用域是在变量书写的那一刻已经确定了. 而`this`的指向是在函数调用的时候才能确定(与函数声明的位置无关)

#### `this的使用`

> `this`是在运行时绑定的,并不是再编写时绑定的,它的上下文取决于函数调用时的各种条件. `this`的绑定和函数的声明的位置没有任何关系,只取决于函数的调用方式  

* 默认绑定:  作为函数进行调用时,非严格模式下,函数内部的`this`指向全局对象
* 隐式绑定: 是否作为对象的方法调用. 有些情况下会使隐式绑定的`this`丢失. 还有是对象的方法作为`setTimeout和setInterval`的参数时,也会丢失
```javascript
const o = {
	a: 123,
	fn: function() {
		console.log(this.a)
	}
}

const bar = o.fn

const a = 'test'

bar()  // test
```

* 显示绑定: 通过`Function.prototype.call(context,...args)和Function.prototype.apply(context, args)`,来修改函数内部的`this`的绑定.
	* 注意这俩个函数的第一参数要求是一个对象, 如果传入原始值(基本类型的值),则会调用对应的包装函数,转化为对应的引用类型. 如果传入`null或者undefined`则会默认的绑定到全局对象
	* 通过这俩种方法还会衍生出另外一种绑定方式: 硬绑定
		* `Fn.prototype.bind(context,...args)`,也可以事现上面的硬绑定
	* 通过一些`api`调用,会自动的帮我们绑定执行的`this`

```javascript
const fn = function() {
	console.log(this.a)
}

const o = {
	a: 12
}
const a = 'test'


// 也是硬绑定
const bar = function(){
	fn.call(o)
}

bar()   // 12

setTimeout(bar, 100)  // 12


// 通过bind 绑定上下文
const _bar = function() {
	foo()
}.bind(o)

// 一些api自动提供的
const arr = ['我是你爸爸','你是我儿子']

arr.forEach(function(item){
	console.log(item, this.a)
},o)

```

* `new`绑定: 
	* 使用`new`来调用构造函数时, 会执行下面的操作
		* 创建(或者构造)一个全新的对象
		* 这个对象会被执行**原型**连接
		* 这个新对象会绑定到函数调用的`this`
		* 如果函数没有返回其他对象,那么`new`表达式中的函数调用会自动返回这个新对象
	* `new`绑定比隐式绑定和硬绑定的优先级优先级都高
	* 但是箭头函数比其他显示绑定和隐式绑定都高(比`new`的优先级也高)
```javascript
function foo(something) {
	this.a = something
}

const o1 = {
	foo: foo
}
const o2 = {}
o1.foo(2)
console.log(o1.a)  // 2
o1.foo.call(o2, 3)
console.log(o2.a)  // 3

const bar = new o1.foo(4)

console.log(o1.a) // 2
console.log(bar.a)  // 4  ,存在一个优先级的问题. 先执行的new ,后函数调用
```

* 判断`this`: 可以按照下面的规则来判断
	* 在`new`中调用(`new`绑定),`this`绑定的是新创建的对象(实例)
	* 通过`apply,call`调用,`this`指向指定的对象
	* 函数再某个上下文对象中调用(隐式绑定),`this`绑定的那个上下文对象
	* 如果上面三种都不是,使用默认绑定. 严格模式绑定到`undefined`,否则绑定到全局对象
* 对象的混入
	* 多态: (在继承链的不同层次名称相同但是功能不同的函数)看起来似乎是从子类引用父类,但是本质上引用的其实是复制的结果

```javascript

function mixin(source, target) {

	for (var key in source) {
		if(!(key in target)) {
			target[key] = source[key]
		}
	}
	return target
}
```
### 类型
#### 类型
* 基本类型有6种: `boolean, number, string, null, undefined, symbol`
* 复杂类型: `object`
	* 复杂类型: `Math, function, Number, String, Boolean, Object, RegExp, Date`
#### 值和类型
* 只有值有类型,变量可以随时持有任何类型的值
* `typeof a`: 检测不是变量的类型, 而是变量的值的类型
* 注意在使用`typeof`检测变量的类型的时候, `undefined undecolared`都会返回`undefined`,这是由于`typeof`本身的安全防范机制导致的.

#### 字符串
> 具有不可变性(字符串的成员方法,都不改变原始值,而是返回新值.)  

* 字符串的方法有:
	*  `str.charAt(index)`: 返回指定位置的值
	* `str.indexOf(val)`: 返回字符串中该值所在的位置(只返回第一个)
		* `str.concat(str1)`: 返回合并后的字符串
	*  `str.split(symbol)`: 按制定的符号切割字符串,组成数组.并返回数组
	*  `str.trim()`: 去除字符串俩头的空格
	*  `str.replace(reg|str,fn|str|)`: 第一个参数为一个匹配的正则或者是要匹配的字符串,第二个参数是一个函数或者是替换的值
	*  `str.match(reg)`: 返回匹配后的数组
	*  `str.slice(start,end)|str.substr(start,num)|str.substring(start,end)`这三个方法不完全相同,注意使用

#### 对象
* `obj.a`: 属性访问, 要求属性的名称符合标识符的命名规则
	* 标识符
		* 以字母,下划线或者美元符号($)开头
		* 由字母,下划线,美元符号($)和数字组成
* `obj['a']`: 键名访问. 可以接受任意`UTF-8/Unicode`字符串作为属性名.
* 对象的浅复制和深复制: 
	* 浅复制: 
		* `ec6`提供的`Object.assign(target, source1,source2....)`
	* 深复制: 
		* `JSON.parse(JSON.stringify(obj))`:  但是方法会被忽略掉
* `Object.defineProperty(obj,property,descriptor)`: 
	* `writable`: 决定是否可以修改属性的值, 为`false`时, 严格模式,对该属性进行赋值时, 会 抛出错误
		* `getter,setter`: 方法可以定义属性的值读写逻辑
		* 对象的响应式原理: 通过`Object.defineProperty`对应属性转化为`getter和setter`,并添加`watcher`来实现, 属性的值改变的时候, 调用相应的`watcher`,来驱动页面的修改
	* `configurable`:  属性是否可以配置, 只要属性是可配置的就可以使用`defineProperty`来修改属性描述符
		* `configurable: false`: 无法通过`delete`删除对象的属性
		* 无法通过`Object.defineProperty()`: 修改对象的`descriptor`
		* 如果对象的属性描述符`writable: true`,则可以修改属性的值
		* `configurable: false`: 无法再修改回去,是单向的
	* `Enumerable`:  设置该属性是否可枚举
		* `enumerable: false`: 不可枚举, 则无法通过`for..in`循环遍历到
		* 可以通过`in和hasOwnProperty`检测到属性是否存在于对象中
		* `obj.propertyIsEnumerable(property)`: 会检测给定的属性名是否直接存在于对象中(而不是原型链中),并且满足`enumerable: true`
		* `Object.keys(obj)`: 会返回一个数组,包含所有可枚举属性(对象自身的可枚举属性, 不会获取原型链上的)
		* `Object.getOwnPropertyNames(obj)`: 会返回一个数组,包含所有属性,无论他们是否可枚举(对象自身的属性,不会获取原型链上的)
* 对象的不可变性: 
	* 对象常量: 结合`writable: false和configurable: false`就可以创建一个真正的常量属性(不可修改,重定义或者删除)
	* 禁止扩展: `Object.preventExtensions(obj)`
		* 使用后,对象将无法添加新属性
	* 密封: `Object.seal(obj)`
		* 这个方法会在一个现有对象上调用`Object.preventExtensions(obj)`并把所有现有属性标记为`configurable: false`, 
		* 使用该方法后 ,对象不能添加新属性,也不能重新配置或者删除任何属性
	* 冻结:  `Object.freeze(obj)` 会创建一个冻结对象
		* 这个方法会在现有对象上调用`Object.seal(obj)`,并把所有数据访问标记为`writable: false`这样就无法修改他们的值
	* `[[Get]]`和`[[Set]]`:  对应属性的`getter和setter`函数
		* 可以通过这俩个函数来控制对象属性的具体读写逻辑
```javascript
const obj = {
	get a() {
		return this._a_
	}
	set a(v) {
		this._a_ = v
	}
}

const obj2 = Object.create(null)

Object.defineProperty(obj2, 'a', {
	get() {
		return this._a_
	},
	set(v) {
		this._a_ = v
	},
	writable: true
})
```


* 存在性: 
	* `'a' in obj`: 检查属性是否在对象及其原型链中
		* `4 in [1,2,4]`:  会返回`false`,再对数组使用时,检测是数组的索引
	* `obj.hasOwnProperty('a')`:  检测一个属性是否存在某一个对象中,不检测原型链
* 遍历性
	* `for...in`循环可以遍历对象的可枚举属性,属性的值要单独的获取
	* `ec6`增加了一个遍历可迭代对象的方法`for…of`,默认遍历的是`value`
	* `ec5`增加了一些数组的辅助迭代器,包括`forEach,every,some,map`
		* `forEach`: 遍历数组中的每一项并忽略回调函数的返回值
		* `every`: 会一直运行直到回调函数返回`false`(跳出循环)
		* `some`:  会一直运行直到回调函数返回`true`(终止遍历)

#### 原型
* 属性设置和屏蔽。属性屏蔽比我们想象中的复杂。下面我们分析下如果`foo`不直接存在于`myObject`中而是存在于原型链上层时`myObject.foo = 'bar'`会出现三种情况
	* 如果在`[[Prototype]]`链上层存在名为`foo`的普通函数访问属性,并且没有标记为只读(`wriatable: true`),那就会直接再`myObject`中添加一个名为`foo`的新属性,它是屏蔽属性
	* 如果在`[[Prototype]]`链上层存在`foo`,但是它标识为只读`wriatble: false`,那么无法修改已有属性或者在`myObject`上创建屏蔽属性. 如果运行在严格模式下 ,代码会抛出一个错误.否则,这条赋值语句会被忽略.总之,不会发生屏蔽
	* 如果在`[[Prototype]]`链上层存在`foo`并且它是一个`setter`那就一定会调用这个`setter`. `foo`不会被添加到 (或者说屏蔽于)`myObject`,也不会重新定义`foo`这个`setter`
	* 但是需要注意的是: 使用`Object.defineProperty()`不受上面的影响

```javascript
const obj = Object.create(null);
   Object.defineProperties(obj,{
     name: {
       value: 'wyt',
       writable: true
     },
     age: {
       value: 18,
       writable: false
     },
     sex: {
       set(v) {
        this._sex_ = v
       }
     }
   })

   const o1 = Object.create(obj)
   o1.name = 'lfl'
   console.log(o1, obj);
   o1.age = 16
   console.log(o1, obj);
   o1.sex = 0
   console.log(o1, obj);

```

* `a.isPrototypeOf(b)`: 判断一个对象是否是另一个对象的原型对象
* `Object.setPrototypeOf(a,PrototypeObj)`:  让一个对象的原型对象设置为指定的对象
* `Object.getPrototypeOf(a)`: 获取一个对象的原型对象
* `a instanceof F`:  检测一个对象是否在一个构造函数的原型上
#### 在使用原型继承的时候
* `Object.setPrototypeOf()`比`Object.create()`性能稍微好点
* `Object.setPrototypeOf(a, b.prototype)`: 把一个对象的原型对象关联到指定的对象
* `const a = Object.create(obj, decriptor)`:  创建一个对象,并把这个对象的原型关联到指定的对象

#### 行为委托

* 委托理论:  委托行为意味着某些对象在找不到属性或者方法引用时会把这个请求委托给另一个对象
* 再`API`接口的设计中,委托最好再内部实现,不要暴露出去
* 互相委托(禁止):  可能为无线循环
* 类风格对象间的关系图
![](%E4%BD%A0%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84JavaScript/%E5%A7%94%E6%89%98%E8%A1%8C%E4%B8%BA.png)

* 对象关联风格代码的思维模型
![](%E4%BD%A0%E4%B8%8D%E7%9F%A5%E9%81%93%E7%9A%84JavaScript/%E5%AF%B9%E8%B1%A1%E5%85%B3%E8%81%94%E5%9B%BE.png)


```javascript
const Task = {
   setId: function(id) {
       this.id = id
    },
    outputId: function() {
       console.log(this.id);
     }
 } 
 const XYZ = Object.create(Task)
  XYZ.prepareTask = function(id, label) {
    this.setId(id)
    this.label = label
  }
  XYZ.outputTaskDetails = function() {
    this.outputId()
    console.log(this.label);
  }
XYZ.prepareTask('22','test')
```

#### 闭包
> 有权访问另一个函数作用域内变量的函数  
* 闭包的好处
	* 能够访问函数定义时所在的词法作用域
	* 私有化变量
	* 模拟块级作用域
	* 创建模块
		* 必须有外部的封闭函数, 该函数必须至少被调用一次(每次调用都会创建一个新的模块实例)
		* 封闭函数必须至少返回一个内部函数,这样内部函数才能再私有作用域中形成闭包,并且可以访问或者修改私有的状态
		
#### 数组
> 有些方法会改变原始值.  

*  改变原始值得有: `push、pop、shift、unshift、splice`
* 不改变原始值得有：`indexOf、concat、join、slice、filter、forEach、map,reduce,findIndex,findItem等`
* `array.reduce(reducer,initVal)`
*    `reducer(init,current,index,arrar)`: 初始值、当前值、索引和数组本身。
*  `initVal`: 可传可不传.不传时,累加重索引1开始,传入时从0开始

#### `class`语法的弊端

* 只能声明方法, 无法声明属性,也就无法实现实例属性共享
* 构造函数中的属性如果和共享方法同名 ,可能会被屏蔽

```javascript
class Person {
	constructor(name) {
		this.name = name
	}
	name() {
		console.log(this.name)
	}
}

const p1 = new Person('wyt')
p1.name   // wyt
```
#### 函数传值的几种类型
> 函数传值有俩种类型: 值和引用  

*  值类型：基本类型的传值
* 匿名函数没有`name`标识符
	* 调试栈更难追踪
	* 自我引用(递归,事件(拆除)绑定,等等)更难
	* 代码(稍微)更难理解
* 引用类型： 其实也属于值类型，只不过复制的指针
```javascript
// 注意在判断一个对象是函数时
//typeof document.createElement( "object" ) === "function" ie9以上返回function
function isFunction(obj) {
  return typeof obj === 'function'&& obj.nodeType !== 'number'
}

```

### 强制类型转换

####  ToString

*  `JSON.stringify(json)`: 再对象中遇到`undefined、function、symbol`时会自动将其忽略，再数组中则会返回`null`(以保证单元位置不变)。如果对象中定义了`toJSON()`方法,`JSON`字符串化时先调用该方法,然后用它的返回值进行序列化.

* 可以接受第二个参数`replacer`,类型可以是数组或者函数.用来制定对象序列化过程中哪些属性应该被处理,哪些应该排除。

	*  可以是一个数组。必须是字符串数组，其中包含序列化要处理的对象的属性的名称，除此之外其他的属性则被忽略
	* 可以是一个函数。他会对象本身调用一次，然后对对象中的每一个属性各调用一次，每次传递俩个参数，键和值。如果忽略某个键就返回`undefined`，否则返回特定的值
  
  

```javascript
  let a = {
      b: 42,
      c: '42',
      d:[1,2,3]
  }
  JOSN.stringify(a,['b','c'])   // "{"b":42,"c":"42"}"
  
  JSON.stringify(a,(k,v)=>{
      if （k !== 'c'） return v
  })    // "{"b":42,"d":[1,2,3]}"
```
####  ToNumber

* `true` 转换为1，`false` 为0。`undefined`为NaN，`null`转换为0
* 对象会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上的转换规则将其强制转换为数字。
* 对象|数组 会首先转换为相应的基本类型值,如果返回的是非数字的基本类型值,调用其`valueOf()和toString()`,再遵循以上的规则将其强制转化为数字

#### *ToBoolean*

* 转化为布尔值为假值的有：`undefined、null、false、 +0、-0和NaN、以及""`
* 隐式转换为布尔值的情况:
	* `if(bol)`
		* for(. . ; bol ;  . .)
	*  `while(bol)和do {}while（bol）`
		* `bol?.."..`
	*  逻辑操作符`||  && `

####  字符串和数字之间的相互转换

* 数字转换为字符串，和`toString（）`没有太大的区别

* 字符串转换为数字。 
  
```js
  // 字符串前面加一些运算符
   ‘+23' // 23  
  ‘-23ad' // NaN
  '1233.3443'|0  //1233
  // 通过Number（str）转换为数字时
  	// 数字    还是数字
  	//true  1   false  0
  	// null 0   undefined NaN
  	// 字符串里面还有非数字时   NaN
  // 通过 parseInt（str）  主要用来转换字符串，其他的类型的数据也会隐士的转换为字符串然后再转换为数字（兼容老版本，第二个参数多少进制。 传10）
  	// 字符串以数字开头的，会一直解析到非数字或者第一个小数点出现为止.其他都是NaN
  parseInt('223adad')  // 223
  // parseFloat（str） 会一直解析到第二个小数点或者非数字      其他为NaN
  
  // 在和NaN进行运算时，结果还是NaN
```  


#### 宽松相等和严格相等

> `==允许在相等比较重进行强制类型转换 ， ===不允许`。由于严格相等，比较的是数据的类型和值，比较理解。下面主要介绍宽松相等`==`  

* 同类型的数据进行比较时,和严格相等没有什么区别
* 字符串和数字之间进行比较时,  字符串转为数字进行比较
* 其他类型和布尔类型之间的相等比较。布尔值转换为数字进行比较
* `null`和`undefined`进行相等比较时，总是相等
* 对象和非对象之间的相等比较。则对象会进行`ToPrimitive（）`，然后再进行比较。
	*  `toPrimitive：`对象为了转换为相应的基本类型的值，会先检查该值是否有`valueOf()`方法。如果有并且返回基本类型的值，就使用该值进行强制类型转换。如果没有就使用`toString()`的返回值（如果存在）来进行强制类型转换。如果上面俩个方法都不返回基本类型的值，就会报错
* 完全运用隐式强制类型转换
	* 如果俩边的值中有`true`或者`false`，千万不要使用`==`、
		* 如果俩边的值中有`[],""或者0`千万不要使用`==`
	* 再其它情况下,使用`==`都是安全的.不仅仅是安全而已,这在很多情况下也会简化代码,提高代码的可读性

#### 抽象关系比较 （> < >= <=等）

> 抽象关系比较分为俩个部分，比较双方都是字符串和其他情况  

* 比较双反首先调用`toPrimitive`，如果结果出现非字符串，就根据toNumber规则将双方强制类型转换为数字进行比较。
* 如果比较双方都是字符串，则按字母顺序进行比较

### 语法

####  语句和表达式的区别

* 表达式：表达式会产生一个值，可以用在任何需要值的地方。比如作为一个函数的调用的参数或作为赋值操作的右值
* 语句：大致上说语句表示了一种行为，如循环和if语句，一个程序基本上是就是语句的序列。


```javascript
[]+{}; // "[object Object]"
{}+[];  // 0

// 第一行代码,{}出现在+运算符表达式中,因此它被当作一个值(空对象)来处理。[]对被强制类型转换为""，而{}会被强制类型转换为[object Object]
// 第二行代码中，{}被当作一个独立的代码块（不执行任何操作）。代码块结尾不需要分号，所以这里不存在语法上的问题。最后的+[]将[]显示的强制类型转换为0；

```
* [js运算符优先级](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)


#### 混合环境JavaScript
* `js`中的保留字分为四类：关键字和预留关键字、`null`常量、`true和false`布尔常量

### 异步和性能
#### 异步控制台
* 并没有什么规范或一组需求制定`console.*`方法族如何工作——它们并不是`JavaScript`正式的一部分，而是宿主环境添加到`JavaScript`的。因此不同的浏览器和`JavaScript`环境可以按照自己的意愿来实现，有时候这会引起混淆。尤其要提出的是，再某些条件下，某些浏览器`consolelog(..)`并不会把传入的内容立即输出。（出现这种情况的主要原因是：再许多程序中，`I/O`是非常低速的阻塞部分。所以，浏览器在后台异步处理控制台`I/O`能够提高性能）
* 介于上面的这种情况,调式的时候,依赖`console`有时候是不行的,可以借助于打断点或者对需要打出的对象执行强制转换`JSON.stringify()`,打印出对象的快照

```javascript
  let a = {index:1}
  console.log(a)
  a.index++
  // 有些浏览器会打印出{index：2}
```

#### Promise
#### 怎么判断一个值是否是一个promise
*  可以通过鸭子类型来检测（根据一个值的形态对这个值的类型做出一些假定）


```javascript
  if (p !== null && (typeof p === 'object' || typeof p === 'function') && typeof p.then === 'function') {
      // 假定这是一个 thenable
  } else {
     // 不是 thenable
  }
```
*  `Promise.resolve（）`：可以接受一个非`Promise`、非`thenable`的立即值，则返回用这个值填充的`promise`。如果传一个非`promise`的`thenable`值，就会试图展开这个值，而且展开过程会持续到提取出一个具体的非类`promise`的最终值。如果传入一个`promise`，就会返回同一个`promise`(可以是拒绝或者完成)。
*  `Promise.reject()`: 不会像`resolve`那样对传入的`thenable和promise`进行展开,而是把传入的值原封不动的设置为拒绝理由。后续的拒绝处理函数接收到的是你实际传给`reject（..）`的那个`promise|thenable`，而不是底层的立即值。
*  `Promise.all([..])`: 参数为一个数组(或者可迭代,里面的值可以是立即值|`thenable|promise`等。如果数组是空的，主`Promise`会立即完成),对于数组里面的值，会用`Promise.resolve()`进行处理。通常有`Promise`实例组成。从`Promise.all([..])`返回的`promise`会接收到一个消息。这是一个有所有传入`promise`的完成消息组成的数组，与制定的顺序一致（与完成顺序无关）。从`promsie.all([..])`返回的主promise在且仅在所有的成员`promsie`完成后才会完成。如果有任何一个被拒绝的话，主`Promise.all[..]`就会立即被拒绝,并丢弃来自其他所有`promise`的全部结果。
*  `Promise.race([..])`: 参数和上面的一样。如果传入的数组是空的,则会一直没有决议,所以不要传空数组。参数里面有一个完成，则立即完成。有一个拒绝，则立即拒绝（参数里面的`promsie`是竟态的）

```javascript
  const p1 = Promise.resolve(42)
  const p2 = Promsie.resolve(31)
  Promise.all([p1,p2]).
  then(res=>{
    // res[0]  42
    // res[1]  31
    let sum = res[0]+res[1]
  },err=>{
    console.log(err)
  })
  
  const p3 = request(url1)
  const delay = function(delay){
    let timerId = setTimeout(()=>{
      timerId = null
      // code ...
    },delay)
  }
  Promise.race([p3,delay(3000)]).
  then(res=>{
    // code...
  },err=>{
    console.log(err)
  })

```
*  一个生成`Promise`的辅助工具

```JavaScript
if(!Promise.wrap) {
  Promise.wrap = function(fn) {
    return function(...args) {
      return new Promise((resolve,reject)=>{
        fn.apply(null,args.concat((res,rej)=>{
          if (rej) {
            reject(rej)
          } else {
            resolve(res)
          }
        }))
      })
    }
  }
}
```

### 生成器
> 生成器对象是由一个 generator function 返回的,并且它符合可迭代协议和迭代器协议。  

```javascript
  function* gen() { 
    yield 1;
    yield 2;
    yield 3;
  }

let g = gen(); 
// 启动生成器
console.log(g.next().value) // 1
console.log(g.next().value) // 2
console.log(g.next().value) // 3
console.log(g.next().value) // undefined
```
#### 生成器的方法
*  `Generator.prototype.next()`：返回一个由yield表达式生成的值
*  `Generator.prototype.return()`：返回给定的值,并结束生成器
*  `Generator.prototype.throw()`： 向生成器跑出一个错误
	* 再调用`next()`方法时,可以传一些值给`yield`表达式,然后进行运算   
  
#### 异步生成器
*  生成器可以和一些异步操作结合起来用(常见的如网络请求和`promise`等)
* 生成器和Promise结合起来一起使用时,只需要yield 一个promise即可（再promise决议里完成时调用next方法，失败调用throw方法即可）

```JavaScript
  function foo (url) {
    // 返回一个promise
    return request(url)
  }
  function *main() {
    let data = yield foo('https://XXXXX')
    
    console.log(data)
  }
  let it = main()
  
  let p = it.next().value
  p.then(res=>{
    it.next(res)
  },err=>{
    it.throw(err)
  })
  
  // ec7 的 async await语法,可以简化上面的操作
```  

#### 生成器的委托
* 生成器内部可以委托,语法是 `yield *iterator`
* 委托时,可以通过`next`方法进行传值(消息也可以委托)
* 错误和异常也可以委托,错误和异常没有主动捕获时,会直接终止迭代,然后抛出异常

```JavaScript
function *foo() {
  console.log('*foo starting)
  
  yield 2;
  yield 3;
  console.log('*foo finshed')
  return 4
}

function *bar() {
  yield 1;
  let text = yield *foo()
  console.log(text)
  yield 5
}
```

#### `let 和 const`
* 不要在用`let`和`const`变量声明前使用,会形成暂时性死区,报错
* 用这俩个声明的变量没有声明提升
* `const`声明的是常量,值不能再改变(`object`,类型不能改变)
* `let`在`for`循环或者`for-in、for-of`中使用时,每次循环都会重新声明变量(之前使用`var`声明循环变量时,需要用立即执行函数保存变量)

#### `spread和rest: 展开和收集`

> 都是通过`...`来表示的,根据位置不同.作用不同  
*  `...`放在数组和对象的前面则是展开(任何`iterable都可以`)

```JavaScript
const a = [1,2,3]
let b = [4,5,...a]
function test(...args) {
  console.log(...args)
}

test(...b)

```

* `...`在函数传参时,可以把传入的实参收集成为一个数组

```javascript
function test(a,...args) {
  console.log(args) // 3,4,5
}

test(1,3,4,5)
```

#### 默认参数值
*  可以给函数的形参设置一些默认值,当该参数缺失或者传入`undefined`时,则取默认值

```JavaScript
  function test(x=11,y=31) {
    console.log(x+y)
  }
  
  test()  // 42
  test(5) //36
  test(5,null)  //5
  test(5,undefined) //36

```

#### 默认值表达式
*  再给形参设置一些默认值,可以使用表达式。注意：表达式中如果使用变量的话，要注意比变量的额作用域。如果使用还没有初始化的变量，会报错

```javascript
const w=1,z=2
function test(x=w+1,y=x+1,z=z+1) {
    console.log(x,y,z);
}
test()  // z is not defined
```    

#### 解构

> 分为数组解构和对象解构。解构可以和`rest`和参数默认值一块使用  

* 数组解构: 根据数组的索引进行解构,可以嵌套,可以使用`rest`

```JavaScript
let [a,b,c] = [1,2,3]  // 1,2,3
let [a,[b],c] = [a,[2],3] //1,2,3
let [a,,b] = [1,2,3]  // 1,3
let [a,...b] = [1,2,3,4] //1, [2,3,4]
```
*  对象解构：根据变量的名称,进行解构,可以给变量名重新命名.主要用于变量的赋值和对参数的解构
	* 在对对象的解构时,一般用于变量的赋值,如果前面省略声明符(`var|let|const`),则赋值表达式必须用小括号括起来
	* 可以嵌套

```javascript

let {x,y,z} = {x:1,a:2,y:3}   // 1,3,undefined

function test({x,y}) {
  console.log(x,y)  //1,2
}
test({x:1,y:2,z:3})

// 注意参数的解构不影响参数设置默认值

function foo({x=5,y=6,z=7}){
  console.log(x,y,z)   // 1,6 7
}

foo({x:1})

let x,y,z


// 如果此处省略小括号,则左边的花括号会被当成块而不是对象
({x,y,z}= {x:1,y:2,z:3})  // 1,2,3

// 可以对变量重新命名
let {x:a,y:b} = {x:1,y:2} // a:1  b:2
```
#mydiary/javascript/advance