# 深入理解ES6
### 块级绑定

####  var 变量的声明和提升
* `var`声明的 变量会提升到当前作用域的顶部, 而初始化的工作会保留在原处. 再初始化之前访问其值为`undefined`
#### 块级声明

> 块级声明就是让所声明的变量再指定块的作用域外无法被访问. 其形式主要有俩种形式, 在函数内部声明的, 在一个代码块(有一个花括号包裹)内部  
* `let声明`:  局部声明.声明的变量没有变量提升, 不能重复声明, 不能再一个块级作用域提前访问该作用域用`let`声明的变量, 会报引用的错误(暂时性死区)
	* 不能重复声明: `var`声明的也不行
* `const`: 常量声明,且声明的变量必须初始化, 声明的变量其值不可更改, 而对于对象,对变量成员的修改并不会报错.`const`阻止的是对于变量绑定与变量自身值的修改,而不是阻止对成员值的修改.因此对于对象,增删属性和修改成员的值不会报错(也就是只要不修改指针,就不会报错).其他特性和`let`一样
```javascript
console.log(typeof value)  // undefined
if(condition) {
	let value = 'blue'
}
// 注意上面的并不会报错
// 暂时性死区只是块级绑定的一个独特表现
```

* `for循环中使用let`:  每次循环都能会创建一个新的同名变量并对其进行初始化. 这样就不用通过立即执行函数来保存变量的值, `let`的这种特性是规范中特别定义的, 和变量提升没有必然的联系
* `for...in || for...of循环使用const||let`:  俩者的区别是, `const`声明的变量再循环内部不能再改变
* 全局块级绑定
	* `var`声明的变量会挂载到全局对象上(`window`), 这样有可能会覆盖全局对象上已有的属性
	* `let||const`: 再全局中生命的变量不会挂载到全局对象上
* 最佳实践: 默认情况下都是用`const `, 如果要修改的话使用`let`

### 字符串和正则表达式(关于码点略过)
#### 识别字符串的方法
* `includes(str1,startPosition)`:  字符串包含指定文本时, 返回`true`, 否则返回`false`
* `startsWith(str1,startPosition)`: 给定文本再字符串起始处返回`true`, 否则返回`false`
* `endsWith(str1,startPosition)`: 给定文本在字符串结尾返回`true`,否则返回`false`, 该方法是从末尾开始检测, 上面的都是从头开始检测
* `repeat(num)`: 重复次数
#### 正则表达式的一些扩展
* 正则表达式`y`标志: 影响正则表达式搜索时的黏连(`sticky`)属性, 他表示正则表达式`lastIndex`属性值的位置开始检索字符串中的匹配字符. 如果在该位置没有匹配成功, 那么正则表达式将停止检索.关于黏连特性的俩个细节
	* 只有调用正则表达式对象上的方法(`exec||test`), `lastIndex`属性才会生效, 作为参数时,则不会生效(字符串的`replace和match`)
	* 当使用`^`字符来匹配字符串的起始处时, 黏连的正则表达式只会匹配字符串的起始处(或者在多行模式下匹配行首). 当`lastIndex`为0时,黏连和不黏连没有任何区别
	* 可以通过声明的正则表达式的`sticky`属性的值检测正则表达式是否有黏连标志
```JavaScript
// 存在黏连标志时, 会从字符串的起始处(没有指定时,lastIndex为0), 如果没有匹配到时, 则不会往下检测
    const str = 'ello1 hello2 hello3'
    const pattern = /hello\d\s?/
    const stickyPattern = /hello\d\s?/y

    let result = pattern.exec(str)
    let stickyResult = stickyPattern.exec(str)
    console.log(result); // hello2
    console.log(stickyResult); // null

    const str = 'hello1 hello2 hello3'
    const pattern = /hello\d\s?/
    const stickyPattern = /hello\d\s?/y

    let result = pattern.exec(str)
    let stickyResult = stickyPattern.exec(str)
    console.log(result); // hello1
    console.log(stickyResult); // hello1   此时的lastIndex的值为7
    console.log(pattern.lastIndex); // 0
    console.log(stickyPattern.lastIndex) // 7
```

* 正则表达式的的复制: `const reg2 = new RegExp(reg1, flag)`,  通过传递给构造函数正则来进行复制, 并且可以增加或覆盖原来的标志
* 正则表达式新增的俩个属性:
	* `reg.source()`: 获取正则表达式的文本
	* `reg.flags()`: 获取正则表达式的标志位

#### 模板字符串
* 多行字符串: 直接换行即可, 模板字符串内的空白符都是字符串的一部分, 因此需要留意缩进
* 制造替换位: `${变量或者表达式}`,模板字面量可以#访问到作用域中任意可访问变量, 访问未定义的会抛出错误
* 模板字面量本身也是表达式, 可以进行嵌套
* 标签化模板: `let mesg = tag `hello world``, 一个模板标签能对模板字面量进行转换并返回最终的字符串值,标签再模板的起始处指定, 标签是一个函数, 第一个参数是一个数组, 里面包含被替换位划分的字符串片段, 后续的参数都是替换位的解释值
```JavaScript
const mesg = ` hello
	world`
console.log(mesg) // hello 
// world
   
let count = 10, price = 0.25,
	message = passthru`${count} items cost $${count*price}.`;	

// 下面的这种转换和模板字面量的默认行为一致, 函数的返回值作为模板字面量的最终输出值, 可以再函数的内部对字面量进行处理  
function passthru(literals, ...substitutions) {
	let ret = ''
	substitutions.forEach((item,i)=>{
		ret += literals[i]
		ret += item
	})
	ret += literals[literals.length-1]

	return ret
	// return 'hello world'
} 
```

### 函数

#### 函数参数默认值
* 函数的参数可以提供默认参数进行初始化, 参数没有传的时候, 会使用默认值
* `EC5`中参数默认值, 比较严谨的是使用`typeof`检测参数值是否是`undefined`, 而不是使用`||`
* `EC5`中, 非严格模式下, 函数里面的参数(除了具名参数)的修改,都会影响`arguments`对象, 具名参数(有默认值的参数)会和`arguments`对象分离. 严格 模式下, 修改函数的参数, 不会影响到`arguments`
* `EC6`中, 使用默认值(具名参数)时, 会和`arguments`分离, 并且参数的修改, 不会影响到`arguments`对象
* 参数默认值表达式:  
	* 函数的默认值可以使用表达式(可以进行函数调用或者运算)
	* 函数的默认值可以接收前面的参数参与运算或者作为函数的参数进行调用. 但是前面的参数表达式不能使用后面的参数(暂时性死区), 因为此时参数还没有初始化.
* 函数参数拥有各自的作用域和暂时性死区, 与函数体的作用域分离, 这意味着参数的默认值不允许访问再函数体内内部声明的任意变量

```JavaScript
function test(first, second = []) {
	console.log(first) // 1
	console.log(second) // []
	console.log(arguments) // 1
}
test(1)

function test(first, second=getValue(first)) {
	console.log(first) // 2
	console.log(second) // 4
	console.log(arguments) // 2
}

function getValue(i) {
	return i * 2
}

test(2)
```
		 
#### 函数的剩余参数: 剩余参数由三个点`...`与一个紧跟着的具名参数指定, 它会是包含传递给函数的其余参数的一个数组
* `EC5`中获取剩余参数`arguments = [].prototype.slice(arguments,1)`
* `EC6`中获取剩余参数`function test(first, ...others){}`
* 使用该语法的限制条件
	* 一个函数只能有一个剩余参数, 并且它必须放在最后
	* 剩余参数不能再对象字面量的`setter`属性中使用,`setter`属性只接受一个参数, 而该语法是不限制参数的数量的
```javascript
	function test2 (first, ...others) {
      console.log(first); // hehe
      console.log(others); // [1,2,3]
      console.log(arguments); // ['hehe', 1, 2, 3]
    }
    test2('hehe', 1, 2, 3)
```

#### 函数构造器的增强能力: 可以再函数的构造器中使用默认参数和剩余参数

```javascript

const test = new Function('age','name=liu', `return age+""+name`)
const test = new Function('...args', `return args`)
// 可以在构造函数中使用默认值和剩余参数
```

#### 扩展运算符: 可以再函数的参数中使用对象展开运算符, 并且没有顺序的限制

```javascript
    const arr = [1, 2, 3, 4]
    function test(name,...args) {
      console.log(args); //[1,2,3,4]
    }

    test('hehe', ...arr)
```

#### EC6 的名称属性
* `EC5`中判断函数是不是听过`new`关键字调用

```JavaScript
	function Person(opts) {
		if(this instanceof Person) {
			this._init(opts)
		} else {
			throw new Error('must use new to init this function')
		}
	}

// 但是上面的的方法可以绕过
const p1 = new Person(opts)
const p2 = Person.call(p1, opts)
```

* `EC6`可以通过检测`new.target`的属性值, 是否是`undefined`,来判断. 当构造函数使用`new`关键字初始化时, `new.target`的属性值被填充为该构造器, 否则为`undefined`

```JavaScript
function Person(opts) {
		if(typeof new.target != 'undefined') {
			this._init(opts)
		} else {
			throw new Error('must use new to init this function')
		}
	}
  Person.prototype._init = function(opts) {
    console.log(opts);
  }
  const opts = {
    name: 'wyt'
  }
// 但是上面的的方法可以绕过
const p1 = new Person(opts)
const p2 = Person.call(p1, opts)

```

#### 块级函数
* `EC5`中是不能再块级`({}函数除外)`中声明函数的
* `EC6`中, 是允许再块级中声明函数的, 但是严格模式和非严格模式有差别
	* 严格模式:  函数会被提升到块级作用域的顶部, 外部访问不到
	* 非严格模式: 函数会被提升到块级所在函数或者全局作用域的顶部, 而非代码块的顶部
```javascript
 'use strict'
 	const flag = true

   if (flag) {
     console.log(test()); // test

     function test() {
       console.log('test');
     }
   }
   console.log(test()); // test is  not defined

 	const flag = true

   if (flag) {
     console.log(test()); // test

     function test() {
       console.log('test');
     }
   }
   console.log(test()); // test
```

#### 箭头函数
* 没有`this,super,arguments,也没有new.target绑定`,这些值有箭头函数所在的函数来决定
* 不能使用`new`调用(内部没有`[[Construct]]`方法)
* 没有原型,没有`arguments`对象, 不能更改`this`,不允许重复的具名参数
* 语法: 
	* 函数体只有一条语句时,可以省略花括号
	* 只有一个参数时,可以省略括号
	* 只有一条语句, 且返回的是一个对象时, 返回的对象要使用括号
```JavaScript
const f1 = name => 'My name is: ' + name
const f2 = name => {
	const ret = getData(name)
	return ret
}

const f3 = (name, age) => {
	console.log(name, age)
}

const obj = {name: 'wyt', age: 18}
const f4 = () => (obj)
```

* 创建立即调用函数表达式
	* 传统的写法, 比较灵活, `EC6`的写法比较固定

```JavaScript
// 传统写法, 函数部分可以加括号, 也可以不加, 也可以整体加括号
const ret = function(name) {
	console.log(name)
	return 'My name is:' + name
}('wyt')

// 箭头函数的写法

const ret = (name => {
	console.log(name)
	return 'My name is:' + name
})('wyt')
```

* 普通函数内都有`this`绑定, 而箭头函数内没有`this`绑定, 取决于外层函数内的`this`
* 尾调用优化:  必须满足三个条件
	* 尾调用不能引用当前栈帧中的变量(意味着该函数不能是闭包)
	* 进行尾调用的函数在尾调用返回结果后不能做额外操作
	* 尾调用的结果作为当前函数的返回值

``` JavaScript
// 没有使用尾调用优化, n值比较大的时候, 可能会导致堆栈溢出
function factorial(n) {
	if(n<=1) {
		return 1
	} else {
		return n * factorial(n-1)
	}
}

function factorial(n, p) {
	if(n<=1) {
		return 1
	} 
	const ret = n * p
	return factorial(n-1, ret)
}
```

### 扩展对象的功能

#### 对象字面量语法的扩展
* 属性初始化简写: 对象进行初始化的时候, 如果`key`的值和`value`变量的名称一样,可以简写. 简写时, 如果作用域没没有对应的变量会报错`XX is not defined`
* 方法的简写: 可以省略`function`和冒号, 简写的方法内可以使用`super`关键字, 普通的写法不可以使用
* 计算属性名:  对象的属性可以使用方括号进行计算, 里面可以是变量或者表达式
```JavaScript
const name = 'wyt'
const lastName = 'last name'
const test = {
	name,
	get() {
		this.name
	},
	[lastName+'is']: name
}
```

#### 扩展的一些新方法

* `Object.is(a,b)`: 判断俩个数据的类型和值是否相等, 是在`===`的扩充, 主要解决`NaN和+0 -0的问题`

```JavaScript
console.log(+0 === -0) // true
console.log(NaN === NaN) // false

Object.is(+0, -0) // false
Object.is(NaN === NaN) // true

// Object.is() 的polyfill的写法
function is (a, b) {
   if (a === b) {
   // 0 -0 +0 其它
       return a !== 0 || 1 / a === 1 / b
   } else {
   // NaN
      return a !== a && b !== b
   }
}
```

#### `Object.assign({}, target1, target2, ....)`: 会对其他的对象的属性进行浅复制, 返回合并后的对象. 如果需要深拷贝的话, 就不能使用此方法了

```JavaScript
const o1 = {
	name: 'wyt'
}
const o2 = {
	age: 15
}
const o3 = Object.assign({}, o1, o2)

function deepCopy(deep) {
	var deep, source = {}, num;
	if (typeof flag === 'object') {
    num = 0
    deep = false
	} else {
     deep = flag
     num = 1
  }
 	var args = [].slice.call(arguments, num)
  args.forEach(function (item, index) {
     for (var key in item) {
        var current = item[key]
        if (typeof current === 'object' && deep) {
          source[key] = extend(deep,current)
         } else{
           source[key] = current
         }
      }
    });
  return source
}
```

#### 原型的一些扩展

* `Object.getPrototypeOf(obj)`: `EC5`中的方法, 获取某一个对象的原型对象
* `Object.setPrototypeOf(obj, prototype)`: 设置某一个对象的原型对象
* 在需要调用原型对象的方法时, 如果原型链比较长, 使用`Object.getPrototypeOf(obj)`可能会报错, 此时可以是使用`EC6`中的`super`关键字,指向当前对象中原型对象

```JavaScript
const person = {
      name: 'wyt',
      greeting() {
        return this.name
      }
    }
    const friend = {
      name: 'liu',
      greeting() {
        return Object.getPrototypeOf(this).greeting.call(this)+ ' Wahaha'
      }
    }
    const friend_2 = {
      name: 'liu',
      greeting() {
        return super.greeting()+ ' Wahaha'
      }
    }
    Object.setPrototypeOf(friend, person)
    Object.setPrototypeOf(friend_2, person)
    const friend_3 = Object.create(friend)
    const friend_4 = Object.create(friend_2)
    console.log(friend_4.greeting()); // liu Wahaha
    console.log(friend_3.greeting()); // 报错, 反复递归, 造成栈溢出
```

### 解构: 使数据访问更便捷

> 解构是一种打破数据解构, 将其拆分为更小部分的过程  
#### 对象解构 
* `let|const|var {name, type}` = {name: xx, type: xxx}

* 再使用解构赋值事, 一定不要忘了初始化(赋值就是初始化), 其实在使用`const`声明变量的时候, 也必须对变量进行初始化
* 也可以使用解构对已经声明的变量进行重新赋值, 但此时赋值表达式要使用括号括起来

* 使用解构赋值时, 表达式的右侧必须有值, 也即不能是`null或者undefined`

* 设置默认值:  为了防止表达式右侧没有对应得值, 可以再左侧设置一些默认值, 默认值只有在对象没有对应的属性(也是`undefined`)或者值为`undefined`时才会生效

* 解构非同名局部变量, 也可以同时设置默认值

* 嵌套解构: 对象嵌套解构赋值,

```JavaScript
let node = {
    type: 'test',
    name: 'test'
}, type = 'init', name = 'init';

({type, name } = node)
console.log(type)  // test
console.log(name)  // test

function outputInfo(value) {
    console.log(value === node)
} 

outputInfo({type, name} = node) // 传入函数的参数是等号有变动值, 但同时可以对变量进行解构赋值

// 设置默认值
const {type, name, value = true} = node
console.log(value)  // true

// 解构非同名局部变量, 这里仍使用上面的node对象

const {type: localType, name: localName = 'bar'} = node

// 嵌套解构赋值
    let node = {
        type: 'test',
        name: 'test',
        loc: {
          start: {
            line: 1,
            column: 1
          }
        }
      }
    let {loc: {start}} = node
    console.log(start) // {line: 1, column: 1}
	let {loc: {start: localStart}} = node
    console.log(localStart) // {line: 1, column: 1}
	console.log(start)  // start in not defined
```

#### 数组解构
* 语法: `let [,second,third] = ['blue','yellow','green']`
* 使用数组解构赋值时, 表达式的右侧要有值对左边的变量进行初始化
* 数组解构也可以作用于上下文, 但时不需要小括号, 这一点与对象的解构不一样
* 使用数组解构赋值时, 右侧必须有值,  与对象的解构同理
* 数组解构也可以使用默认值, 和对象解构相同
* 数组解构也可以嵌套
* 不定元素, 再数组解构中可以像不定参数那样 , 收集剩下的值. 但是不定元素必须为解构的最后一个值
* 混合解构, 也就是对象解构和数组解构混合使用
* 解构函数参数, 并设置默认值. 结构的参数如果没有传时, 会报错, 为了防止报错, 函数的参数设置默认值
```JavaScript
// 使用数组解构交换俩个变量的值

let a = 1, b = 2
[b,a] = [a, b]

console.log(a)  // 2
console.log(b)  // 1

// 使用默认值
let colors = ['blue']
let [firstColor, secondColor = 'green'] = colors

//  作用于上下文
let firsrColor = 'red'
[firstColor] = colors
console.log(firstColor) // blue

// 嵌套解构
const colors = ['blue',['green']]
let [,[secondColor]] = colors
console.log(secondColor) // green

// 不定元素

const colors = ['blue', 'red', 'white']
let [firstColor, ...restColors] = colors
console.log(restColors) // ['red', 'white']

// 混合解构
const node = {
    type: 'test',
    name: 'ec6',
    loc:{
        start: {
            line: 1,
            column: 1
        },
        end: {
            line:1,
            column: 4
        }
    },
    range: [0, 3]
}

let {loc: {start}, range: [startIndex]} = node
console.log(start) // {line: 1,column: 1}
console.log(startIndex) // 0

// 解构函数的参数

// 参数都必传时
function test(val, {name, age}) {
    ...
}
  // 不确定时, 后面的配置对象即使没有值也会传一个空对象
function test(val, {name = 'wyt', age = 18}) {
    ...
}
// 后面的配置对象有可能不传    
function test(val, {name = 'syt', age= 18} = {}) {
    ...
}    
// 也可以设置参数的默认值, 其实这种和上面的重复了 
function test(val, {name = 'syt', age= 18} = {name: 'liu', age: 'heheh'}) {
    ...
} 
```

### 符号与符号属性

> 用于创建不可枚举的属性, 并且这些属性在不引用符号的情况下是无法访问的, 而且这些属性难以被修改  

#### 创建符号值: `const property = Symbol(description)`
* `Symbol`: 基本类型, 符号没有字面量形式(没有具体的值). 可以添加符号描述, 便于调式(但是只有显示或隐式的调用符号的`toString()`方法时, 才会显示其描述文字)
* 符号类型无法转换成其它数据类型
	* `symbol`原始值不能转换为字符串(只能先转换为它的包装对象,再调用`toString()方法`)
* 可以再使用计算属性的地方使用,此外还可以在`Object.defineProperty()||Object.defineProperties()`使用

```JavaScript
const firstName = Symbol('first name')
const person = {
	[firstName]: 'test'
}

const s1 = firstName + ''  // 抛出错误
Object.defineProperty(person, firstName, {writable: false})

const lastName = Symbol('last name')
Object.defineProperties(person, {
	[lastName]: {
		value: 'wyt',
		writable: false
	}
})

console.log(person[firstName]) // test
console.log(person[lastName])  //  wyt
```

#### 共享符号值: 符号可以通过使用`Symbol.for(descritor)`来全局查找已注册的符号, 若存在, 则直接返回. 不存在, 则创建新的. 此外可以通过`Symbol.keyfor(symbol')`从全局检索出其对应的描述值

```JavaScript

	const firstName = Symbol('first name’);
  	const secondName = Symbol('first name’);
  const thirdName = Symbol.for('first name')
  const forthName = Symbol.for('first name')
  console.log(firstName === secondName);  // false
  console.log(thirdName === forthName) // true

  console.log(Symbol.keyFor(firstName)) // undefined
  console.log(Symbol.keyFor(thirdName)); // first name
```

#### 检索符号属性

* 普通属性: 可以通过`Object.keys(obj)||Object.getPropertyNames(obj)`返回属性组成数组
* `Ec6`新增`Object.getOwnPropertySymbols(obj)`: 返回属性是`symbol`类型的组成的数组

#### 使用知名符号暴露内部方法

* `Symbol.hasInstance`: 供`instanceof`运算符使用的一个方法, 用于判断对象继承关系
* `Symbol.isConcatSpreadable`: 一个布尔类型值, 在集合对象作为参数传递给`Array.prototype.concat()`方法时, 指示是否将该集合的元素扁平化
* `Symbol.iterator`:  返回迭代器的一个方法
* `Symbol.match`: 供`String.prototype.match()`函数使用的一个方法,用于比较字符串
* `Symbol.iterator`: 供`String.prototype.replace()`函数使用的一个方法, 用于替换字符串
* `Symbol.iterator`: 供`String.prototype.search()`函数使用的一个方法, 用于定位字符串
* `Symbol.species`: 用于产生派生对象的构造器
* `Symbol.split`: 供`String.prototype.split()`函数使用的一个方法,用于分割字符串
* `Symbol.toPrimitive`: 返回对象所对应的基本类型值的一个方法
* `Symbol.toStringTag`: 供`String.prototype.toString()`函数使用的一个方法,用于创建对象的描述信息
* `Symbol.unscopables`: 一个对象, 该对象的属性指示了那些属性名不允许被包含在`with`语句中
* 上面的知名符号可以通过上面提供的方法或者属性可以修改对象的一些行为

```JavaScript
const p1 = {
    0: 11,
    1: 22,
    length: 2,
    [Symbol.isConcatSpreadable]: true
  }
  const p2 = {
    0: 33,
    1: 44,
    length: 2,
    [Symbol.isConcatSpreadable]: false
  }
  const p3 = [55]
  console.log(p3.concat(p1));
  console.log(p3.concat(p2));

  const arr = new Array('1')
  console.log(arr instanceof Array);  // true

  Object.defineProperty(Array,Symbol.hasInstance,{
    value: function(i) {
      return false
    }
  })

  const arr2 = new Array('2')
  console.log(arr2 instanceof Array); // false
```


### Set与Map

#### Set:  无重复值的有序列表
* 通过`const set = new Set([k1, k2,...])`来创建, 也可以传入一个数组直接进行初始化
* `set.add(val)`:  往列表中添加值
* `set.size`: 返回列表的长度
* `set.has(v)`: 检测列表中是否包含某一个值
* `set.delete(v)`: 删除某一个值
* `set.clear()`: 清空整个列表

#### Set上的`forEach`方法
* 由于`set`没有键, 因此回调函数的前俩个参数始终相等 ,其它与普通的数组一样

```JavaScript
  const arr = [1,45,56,2,34,2,5]
  const set = new Set(arr)
  set.forEach((item, i, context) =>{
    console.log('item', item);
    console.log('i', i);
    console.log('context', context);
  })
```

#### `set`集合对象转化成数组
* `const arr = [...set]`,可以把一个`set`里面的值站展开并作为数组的项
* 使用上面的方法可以给数组去重
```JavaScript
	let arr = [1,3,4,3,1,34,1]
	arr = [...new Set(arr)]
```

#### weak set

> 当`set`对像中保存的有对象的引用时, 即使外面该对象的引用已经销毁, 但是`set`对象中保留的有副本(也就是强引用), 造成外面的对象一直无法释放, 无法被垃圾回收机制收回, 造成内存泄露, 但是`wekset`则不会影响垃圾回收  

* `weakset`集合中的值都是对象, 但是是对对象的弱引用,不影响垃圾回收机制
* 只有`add, delete, has`方法, 但是直接收对象的引用
* 和`Set和WeakSet`实例的差异
	* 对于`WeakSet`的实例, 若调用`add()`方法时传入了非对象的参数, 就会抛出错误(`has()和delete()`则会在传入了费对象的参数时返回false)
	* `WeakSet`的实例无法迭代, 因此不能用在`for-of`循环中
	* `WeakSet`无法暴露出任何迭代器
	* `WeakSet`没有`forEach()`方法
	* `WeakSet`没有`size`属性
```JavaScript
  const set = new Set()
  const weakSet = new WeakSet()
  let t = {
    name: 1
  }
  set.add(t)
  weakSet.add(t)
  t = null

  console.log(set.has(t));  // true
  console.log(weakSet.has(t));  // false
```

#### Map:  键值对的有序列表, 而键和值都可以是任何类型(和`set`对象的值不重复一样使用的是`Object.is()`来做比较的)

* 初始化: `let map = new Map([[k1,v1],[k2, v2]....])`

* 添加和获取: 
	* `map.set(key, value)`: 添加一个键值对
	* `map.get(key)`: 获取指定键的值
* 与`Set`共享的几个方法
	* `has(key)`: 判断指定的键是够存在于`Map`中
	* `delete(key)`: 移除对象中指定键以及对应的值
	* `clear()`:  移除对象中所有的键和值
	* `size`: 返回对象拥有的键值对数
* `Map`对象上的`forEach`方法
	* 第一个参数: 对象中下个位置的值, 第二个参数: 该值所对应的键,第三个`map`只身, 基本和数组比较像
	* 和`set`对象不一样 , 因为`set`对象没有键

```javascript
    const map = new Map([['name','wyt'],[1,true]])
    map.forEach((v,k)=>{
      console.log(v, k);
    })

    map.set('5', 't')
    map.set(5, 'h')
    console.log(map.get(5) === map.get('5')); // false
    console.log(map.size()); // 4
    map.delete('name')
    console.log(map.has('name')); // false
```

#### `Weak Map`:  和`weakSet`一样, 都是存储对象弱引用的方式. 再`weakMap`中的键都必须是对象, 而且这些对象都是弱引用, 不会干扰垃圾回收

* 需注意的是, `weakMap`的键才是弱引用, 而值不是. 因此如果值是引用类型, 也会阻止垃圾回收, 即使该对象的其他引用已全部被移除
* `weakMap`类型是键值对的无序列表,其中键必须是非空的对象, 值可以是任意类型
*  通过`set()`方法添加数据, 通过`get()`方法获取数据

```JavaScript
let map = new WeakMap(), element = documet.querySelector('.element')

map.set(element, 'Original')
let value = map.get(element)
console.log(value)

element.parentNode.removeChild(element)
element = null

console.log(map.has(element))

console.log(map.get(element))  // 报错,由于element不在指向对象, 则对应的map对象会自动切断对应的引用, 以便垃圾回收
```

* `weakMap`和`weakSet`类似, 也没有`size`属性
* `weakMap`初始化:  和`Map`初始化相似, 唯一不同的是, 传入的数组的第一项必须是非空(`null`)的对象, 第二个则可以是任意类型
* `weakMap`集合对象还支持`has()和delete()`方法,但是不支持`clear()`方法(因为不能迭代,所以不能清除所有的键值对,`weakSet`同理)
* `weakMap`大多用于存储`DOM`, 也可以用于对象数据私有化
* `Set和Map`都是无序的, 这一点和数组不一样. 

```JavaScript
let Person = (function() {
    let privateData = new WeakMap()
    function Person(name) {
        privateData.set(this, {name: name})
    }
    
    Person.prototype.getName = function() {
        return privateData.get(this).name
    }
})()

// 外面是无法修改对象里面的数据的
```



### 迭代器(Iterator)和生成器(Generator)

####  迭代器

* 所有的迭代器都有`next()`方法, 返回一个对象`{value: xx, done: boolean}`
	* `value`: 此次迭代的值, 迭代结束时返回最终的值, 没有返回`undefined`
	* `done`: 迭代的标志, 迭代是否结束, `true`迭代结束

####  生成器

* 返回迭代器的函数: `function *generator(){ yield xx; ...}`
* `yield`: 关键字的使用限制
	* 只可在生成器内部使用, 再其他地方使用会导致程序抛出语法错误(再生成器内部不要使用函数的内部, 因为`yield`无法穿越函数边界)
	* 生成器函数表达式: `const createInterator = function * (items){...}`,但是不能将箭头函数创建为生成器
	* 由于生成器就是一个函数, 因此可以作为对象的方法, 但是不要忘记`*`号
```JavaScript
function *createIterator(items) {
	items.forEach(function(item){
		// 语法错误
		yield item + 1
	})
}

// EC5 作为对象的方法

var o = {
	createIterator: function * (items){
		//...code
	}
}

// EC6 作为对象的方法
const o = {
	*createInterator(items) {
		// ...code
	}
}
```


#### 可迭代对象与`for-of`循环

> 迭代对象(`iterable`): 是包含`Symbol.iterator`(该属性定义了为指定对象返回迭代器的函数)属性的对象. 所有的集合对象(数组,`Set与Map`)以及字符转, `arguments和收集运算符`,还有`dom`集合都是可迭代对象, 迭代对象可以配合`for-of`循环使用  

* 生成器创建的所有迭代器都是可迭代对象, 因为生成器默认就会为`Symbol.iterator`属性赋值
* `for-of`: 每次循环调用迭代对象的`next()`方法, 并把结果对象的`value`存储在一个变量上, 循环持续到`done`的结果变为true
* 在不可迭代对象, **null或者undefined**上使用会抛出错误
* 如果一个对象的`Symbol.iterator`属性是一个函数, 则该对象是可迭代对象, 因此让一个普通的对象是可迭代的, 只需要让该属性返回一个函数即可

```JavaScript
// 如果一个对象是可迭代对象, 则其Symbol.iterator 返回迭代函数
	function forOf(iterable,fn) {
    if(!isIterable(iterable)) return iterable
    const iterator = iterable[Symbol.iterator]()
    let next = iterator.next()
    while(!next.done) {
      fn(next.value)
      next = iterator.next()
    }
  }
  function isIterable(i) {
    return typeof i[Symbol.iterator] === 'function'
  }
```

#### 内置的迭代器

* `EC6`拥有三种集合对象类型: `数组, Set, Map`, 这三种类型都拥有如下的迭代器
	* `entries()`: 返回一个包含键值对的迭代器
	* `values()`: 返回一个包含集合中的值的迭代器
	* `keys()`: 返回一个包含集合中的键的迭代器
* 数组和`Set`对象集合没有指定使用特定的迭代器时,默认使用的是`values()`迭代器, 而`Map`集合对象默认的迭代器是`entries()`
* 此外字符串和`dom`集合都有自己内置迭代器, 以便用于`for-of`循环
* 展开运算符可以作用于任何可迭代对象, 因此可以通过展开运算符将迭代对象转化为数组
```javascript
   const map = new Map([['name','wyt'],['age', 1]])
    for(const k of map){
      console.log(k);
    }
    for(const k of map.entries()){
      console.log(k);
    }
    for(const k of map.keys()){
      console.log(k);
    }
    for(const k of map.values()){
      console.log(k);
    }
// 迭代对象的entries, 在使用for of循环迭代时,可以传入key
for(const [k,v] of itetarable.entries) {
	console.log(k,v)
}
```

#### 迭代器的高级功能
* 传递参数给迭代器:迭代器可以将值传递出来, 通过`next()`方法或者在生成器上使用`yield`都可以. 可以通过`next()`方法向迭代器传递参数,该参数就会成为内部`yield`语句的值
	* 第一次传参无效的原因: 上面`yield`语句的值, 具体指的是上次生成器中终端执行处的语句, 而第一次调用没有所谓这样的语句, 因此第一次传参无效 
* 迭代器中抛出错误:
	* 可以通过`try...catch`来捕获生成器内部的错误, 也可以通过`iterator.throw(new Error(...))`来主动抛出错误,中断代码的执行
* 生成器的`return语句`: 
	* 生成器内部的`return`, 意味着迭代结束, 没有返回值则返回`{done: true, value: 'undefined'}`, 有返回值时, 会替换掉`value`的值
	* 扩展运算符与`for-of`循环会忽略`return`语句所指定的任意值.

```JavaScript
	function *creteInterator() {
      let third
      let first = yield 1
      let second = yield first + 2
      try{
        third = yield second + 3
      }catch(e) {
        third = 9
      }
      yield third + second
      return first+second+third  
    
    }
    const iterator = creteInterator()
    console.log(iterator.next());
    console.log(iterator.next(1));
    console.log(iterator.next(2));
    console.log(iterator.throw(new Error('test')));
    console.log(iterator.next(4));
``` 

#### 生成器委托

* 生成器可以再另一个生成器中配合`yield`关键字一起使用,中间需要加`*`号,这样生成器就会在其他的生成器里面迭代
* `yield *fn1()`返回的应该是该生成器对应的迭代器
* 注意`return`关键字只在最外层函数里面才是迭代结束, 嵌套的生成器有`return`关键字时, 所起的作用和普通的函数一样

```JavaScript
    function *fn1() {
      yield 1
      yield 2
      return 3  
    }
    function *fn2(){
		let ret = yield *fn1()
      return  ret
    }

    const iterator = fn2()
    console.log(iterator.next());
    console.log(iterator.next());
    console.log(iterator.next());
``` 

#### 生成器可以执行异步任务
* 和一般异步操作一起使用
* 和`Promise`结合使用, 来控制异步流
* `EC7`的`async函数`

```javascript
function run(gen){
  var g = gen()

  function next(data){
// 拿到迭代的对象值
    var result = g.next(data)
// 迭代结束
    if (result.done) return result.value
// 异步操作
    result.value.then(function(data){
		// 迭代没有结束, 进行递归
      next(data)
    })
  }
  next()
}


// 和promise 结合使用

function run(gen) {
      var args = [].slice.call(arguments, 1),
        it;
      it = gen.apply(this, args);
      return Promise.resolve().then(function handleNext(value) {
        var next = it.next(value);
        return (function handleResult(next) {
          if (next.done) {
            return next.value
          } else {
            return Promise.resolve(next.value).then(handleNext, function handleErr(err) {
              return Promise.resolve(it.throw(err))
                .then(handleResult);
            });
          }
        })(next)
      });
    }

// EC7 更加方便
const asyncReadFile = async function (){
  const f1 = await readFile('/etc/fstab')
  const f2 = await readFile('/etc/shells')
  console.log(f1.toString())
  console.log(f2.toString())
}
```

### JS中的类

#### `EC6`中的类和`EC5(自定义类型)`的区别
* 类声明不会提升,这与函数定义不同.
* 类声明中的所有代码会自动运行在严格模式下, 并且无法退出严格模式
* 类所有的方法都是不可枚举的,  这是对于自定义类型的显著变化(需要手动设置`Object.defineProperty()`)
* 类所有的方法内部都没有`[[Construct]]`, 因此使用`new`来调用他们会抛出错误
* 调用类构造器时不使用`new`, 会抛出错误
* 试图在类的方法内部重写类名, 会抛出错误
* 类只能声明共享的方法, 无法像构造函数一样,声明共享属性 __
```javascript
// 函数声明
class Person{
     constructor(name, age) {
       this.name = name
       this.age = age
     }
     getName() {
       console.log(this.name);
     }
     static extend(o) {
       console.log(o);
     }
   }
// 带有具名的表达式
   let Animal = class Animal_1{
     constructor(name) {
       this.name = name
     }
     getName() {
       console.log(this.name);
     }
   }
// 匿名表达式
	let Animal1 = class {
     constructor(name) {
       this.name = name
     }
     getName() {
       console.log(this.name);
     }
   }
   console.log(Animal.name);  // Animal_1
	console.log(Animal1.name)   // Animal1

// 类中的所有的方法是不可枚举的,和传统的构造函数一样
class Person {
    constructor(name, age) {
        this.name = name
        this.age = age
    }
    getName() {
        return this.name
    }
    getAge() {
        return this.age
    }
}
const p1 = new Person(‘wyt’, 18)

for (const k in p1) {
    console.log(p1[k]);
}
// wyt 18


```

#### 一等公民 类
* 类和函数一样, 可以用于参数, 赋值和函数返回值, 以及其他的场景. 只不过多了`class`关键字
```JavaScript
// 自调用类
let person = new class {
	constructor(name) {
		this.name = name
	}

	sayName() {
		console.log(this.name)
	}
}('wyt')
```

#### 访问器属性
* 自有属性可以再类构造器中创建,此外还可以在原型上定义访问器属性
```JavaScript
class CustomElement {
     constructor(el) {
       this.el = el
     }

     set html(v) {
        this.el.innerHTML = v
     }
     get html() {
       return this.el.innerHTML
     }
   }

   const descriptor = Object.getOwnPropertyDescriptor(CustomElement.prototype,'html')
   console.log(descriptor.enumerable);  // false
   console.log('set' in descriptor);    // true
```

#### 需计算成员名
* 类和对象一样 , 类的方法和类访问器属性也都能使用计算属性
```JavaScript
 const METHODNAME = 'sayName'
   const KEYS = 'keys'
   class Person{
     constructor(name) {
       this.name = name
       this.keys = []
     }
     [METHODNAME]() {
       console.log(this.name);
     }
     set [KEYS] (v) {
       this.keys.push(v)
     }
     get [KEYS] () {
       return this.keys
     }
   }
```


#### 类中的静态方法

* 类中的静态方法是通过`static`关键字来声明的
* 不可再实例中访问静态成员, 必须要直接在类中访问静态成员

```JavaScript
class Person{
    constructor(name) {
        this.name = name
    }
    
    static create(name) {
        return new Person(name)
    }
    
    sayName() {
        console.log(this.name)
    }
}
```

#### 继承与派生类

* 一个类继承另一个类, 是通过`extends`关键字来实现的
* 继承自其它的类被称为派生类, 如果派生类中指定了构造函数则必须要调用`super()`,否则报错.如果不使用构造函数,则当创建实例时, 会默认把所有的参数传入, 但是一般类都要有自己的自由属性,因此大多数都要自己调一下`super`
* 只可在派生类的构造函数中使用`super()`, 如果在非派生类中使用会报错
* 再构造函数访问`this`之前一定要调用`super()`(负责初始化实例)
* 如果不想调用`super`,则唯一的方法是让构造函数返回一个对象

```JavaScript
class Rectangle {
    constructor(length, width){
        this.length = length
        this.width = width
    }
    getArea() {
        return this.length*this.width
    }
}

class Square extends Rectangle{
    constructor(length) {
        super(length, length)
    }
}

const square = new Square(3)
console.log(square.getArea())   //9
console.log(square instanceOf Rectangle)  // true


class Square extends Rectangle{}
// 上面的等同于
class Square extends Rectangle{
    constructor(args) {
        super(...args)
    }
}
```

#### 类方法屏蔽 : 
* 类中的方法会屏蔽父类中同名的方法.  
* 如果对象无法响应某个请求,它会把这个请求委托给它的构造器的原型
#### 类方法的弊端
* 类中不能声明共享的属性,只能声明共享的方法
* 构造函数中的属性名和共享的方法名一样时, 会屏蔽方法名. 没有构造函数灵活

#### 静态成员继承

* 如果基类中有静态成员, 则这些方法会被派生类继承(注意是派生类可以使用基类的静态方法,不是实例使用)

```JavaScript
 class Person{
     constructor(name) {
       this.name
     }

   static create() {
       console.log(123)
     }
   }

   class Male extends Person{
     constructor(name) {
       super(name)
       this.sex = 'male'
     }
     getSex() {
       console.log(this.sex)
     }
   }


   const p = Male.create()
   console.log(p);
```


#### 派生自表达式的类
* 只要一个表达式能够返回一个具有`[[Construct]]`属性以及原型的函数, 就可以被其他的派生类继承
* 任意表达式都能在`extends`关键字后使用,但如果是`null||生成器函数`则会 报错, 因为他们没有`[[Construct]]`
* 因此可以根据上面的特性, 自定义基类, 提供了更多的操作性
```javascript
let SerializableMixin = {
      serialize() {
        return JSON.stringify(this)
      }
    }
let AreaMixin = {
     getArea() {
        return this.length * this.width
     }
}
    const mixin = function(...mixins) {
      const base = function() {};
      Object.assign(base.prototype, ...mixins)
      return base
    }

    class Square extends mixin(AreaMixin, SerializableMixin) {
      constructor(l) {
        super()
        this.length = l
        this.width = l
      }
    }

    const s = new Square(3)
    console.log(s.getArea());  // 9
    console.log(s.serialize()); // {'length': 3, 'width': 3}
```

#### 可以继承内置对象
* `EC5`是不能直接继承内置对象的, 并且也会有差异. 而`EC6`则可以, 因为初始化实例, 是先在基类中初始化, 然后派生类中进行装饰或修改, 这和之前是不一样的
* `Symbol.species`属性:

> 该属性被属于定义一个能返回函数静态访问器属性. 每当类实例的方法(构造器除外)必须创建一个实例时, 前面返回的函数就被用为新实例的构造器  
>   
	* 任意能返回内置对象实例的方法, 在派生类中却会自动返回派生类的实例
	* 拥有该属性的有`Array, ArrayBuffer, Map, Promise, RegExp, Set, 类型化数组`
	* 可以手动控制返回类的类型

```JavaScript
 function MyArray() {
  	Array.apply(this, arguments)
}

 MyArray.prototype = Object.create(Array.prototype, {
   constructor: {
     value: MyArray,
     writable: true,
     enumerable: true,
     configurable: true
   }
 })

 const a = new MyArray()
 a.push([1,2,3]);
 const s = a.slice(1);
 console.log(s instanceof MyArray); // false

class MyArray extends Array{

}
const a = new MyArray()
a.push([1,2,3]);
const s = a.slice(1);
console.log(s instanceof MyArray); // true



class MyArray extends Array{
      static get [Symbol.species]() {
        return Array
      }
    }
    class SArray extends Array{

    }
    const a = new MyArray()
    const b = new SArray()
    a.push([1,2,3]);
    b.push([1,2,3])
    const s = a.slice(1);
    const h = b.slice(1)
    console.log(s instanceof MyArray); // false
    console.log(s instanceof Array);  // true
    console.log(h instanceof SArray); // true
    console.log(h instanceof Array);   // true
```

####  再类的构造器中使用 `new.target`
* 一般情况下`new.target`等于类本身
* 主要用来判断, 类是否是通过`new`关键字来调用. 如果使用`class`语法默认是要求必须使用`new`来调用的

```JavaScript
function Person(name) {
      if (new.target === undefined) {
        throw new Error('please use new to init')
      }
      this.name = name
  }

    const p = Person('wyt')  // 抛出错误
```

### 增强的数组功能

#### `Array.of(a, b, c ...)`: 接收的参数作为新创建数组的项

* `EC5`中,通过构造函数创建数组的时候,有个坑, 就是如果只传一个参数,当这个参数时数字类型时, 这个数字是创建的数组的`length`的值. 而传其他类型的时候, 则是作为数组唯一的项. 传入多个参数的时候, 则只会作为数组的项
* 该方法接收的所有参数,都只作为数组的项

```JavaScript
// ec5中的问题
const a = new Array(2)
const b = new Array('2')
console.log(a.length)  // 2
console.log(b.length)  // 1

// ec6

const c = Array.of(2)
const d = Array.of('2')

console.log(c.length)  // 1
console.log(d.length)  // 1
```

#### `Array.from(arrlike,fn,context)`:可以把类数组转换为真正的数组

* `EC5`中, 把类数组转化为数组,常采用的方式为`[].slice.calll(arrayLike)`
* `EC6`则可以通过上面的方式创建, 并且还可以对类数组的每一项做处理(新的映射)
* 第三个参数,可以指定上下文
* 不过`ec6`还有展开运算符和收集运算符
* 并且该方法还可以用在可迭代对象上(包含`Symbol.interator`属性的对象)

```javascript
    function translate() {
      return Array.from(arguments, (v)=> v+1)
    }
    const a = translate(1,2,3)
    console.log(a);
```

#### 数组其他的新方法
* `array.findIndex((item,index,array)=>{},context)`
* `array.find((item,index,array)=>{},context)`
	* 上面的方法接收的参数和数组`map,forEach,filter`一致, 函数里面是定义我们要查找的条件, 返回满足条件的第一个项||索引, 如果没有满足条件的则返回`-1(findIndex) || undefined(find)`
```javascript
const arr = [1,3,5,7,8]
      const fn = (v) => v >5
      const noFn = v => v >10
      const _index = arr.findIndex(fn)
      const _item = arr.find(fn)
      const _noIndex = arr.findIndex(noFn)
      const _noItem = arr.find(noFn)

      console.log(_index, _item); // 3  7
      console.log(_noIndex, _noItem); // -1 undefined
```

* `array.fill(fillVal[, startIndex][, endIndex])`
	* 第一个参数为填充的值, 第二个参数为要填充的起始的所引, 第三个参数为结束索引(不包含结束位置)
	* 如果起始或者结束为负值, 则会和数组的所引相加后的值
```JavaScript
		const arr_1 = [1,3,5,7,8]
      const arr_2= [1,3,5,7,8]
      const arr_3 = [1,3,5,7,8]
      const arr_4 = [1,3,5,7,8]
      const arr1 = arr_1.fill(2)
      const arr2 = arr_2.fill(2,2)
      const arr3 = arr_3.fill(2,2, 4)
      const arr4 = arr_4.fill(2, -2)
      console.log(arr1, arr2, arr3, arr4);
```

* `array.copyWith(fillIndex, copyIndex, num)`
	*  第一个参数为`copy`起始的位置, 第二个参数是复制起始的位置, 第三个参数是要覆盖的项数
	* 该方法可以修改数组内部的值(复制数组内部的一些值,覆盖其他的值)

```javascript
		const arr = [1,2,3,4,5,6]
     	console.log(arr.copyWithin(2,5));  //[1, 2, 6, 4, 5, 6]
     	console.log(arr.copyWithin(2,4));  //[1, 2, 5, 6, 5, 6]
     console.log(arr.copyWithin(2,3));  // [1, 2, 6, 5, 6, 6]
     console.log(arr.copyWithin(2,3,1)); // [1, 2, 6, 5, 6, 6]
```

### `Promise`与异步编程

> 关于`js`的`event loop`可以参考其他的笔记, `js`是单线程, 事件驱动的. 有一个主线程,还有一个任务队列. 主线程主要执行同步的代码, 任务队列则主要放置那些异步的(异步执行有结果的时候, 才会放置再任务队列)和监听的事件(触发后),才会放置再任务队列中, 并且遵循先进先出.只有主线程的代码执行完, 才会执行任务队列  

#### 事件模型
* 事件模型, 必须触发后才会执行
#### 回调模型
* 对于异步的操作, 通过传回调参数,来执行截下来的操作.复杂的时候,可能会有多层回调嵌套, 容易造成回调低于(`callback hell`)
#### `Promise`
* `promise`对象的状态不受外界的影响(异步执行的结果决定)
	* 有三总状态, 只有异步操作的结果可以决定当前是哪一种状态，其他任何操作都无法改变这个状态
	* `Promise`的状态一旦改变，就不会再变，任何时候都可以得到这个结果，状态不可以逆，只能由 `pending`变成`fulfilled`或者由`pending`变成`rejected`
	* `pending`: 初始状态
	* `fulfilled`: 成功状态
	* `rejected`: 失败状态
* `Promise`的三个缺点:
	* 无法取消Promise,一旦新建它就会立即执行，无法中途取消
	* 如果不设置回调函数，Promise内部抛出的错误，不会反映到外部
	* 当处于pending状态时，无法得知目前进展到哪一个阶段，是刚刚开始还是即将完成
* ` Promise`在哪存放成功回调序列和失败回调序列？
	* `onResolvedCallbacks` 成功后要执行的回调序列 是一个数组
	* `onRejectedCallbacks` 失败后要执行的回调序列 是一个数组
	* 以上两个数组存放在`Promise` 创建实例时给`Promise`这个类传的函数中，默认都是空数组。每次实例`then`的时候 传入` onFulfilled `成功回调` onRejected` 失败回调，如果此时的状态是`pending `则将`onFulfilled`和`onRejected push`到对应的成功回调序列数组和失败回调序列数组中，如果此时的状态是`fulfilled` 则`onFulfilled`立即执行，如果此时的状态是`rejected`则`onRejected`立即执行
	* 上述序列中的回调函数执行的时候 是有顺序的，即按照顺序依次执行
* 创建未决的`Promise`
	* 通过`promise`的构造器来创建
	* `Promise`的执行器会立即执行(同步的), `resolve和reject`是异步的
```JavaScript
function request(url, data) {
	return new Promise((resolve, reject)=>{
		axios.then((res)=>{
			resolve(res)
		}).catch((err)=>{
			reject(err)
		})
	})
}
```

* `then(resolveCallback, rejectCallback)`:  `then`函数接收俩个回调, 一个是成功状态的回调, 一个是失败状态的回调. 失败的回调可以被`catch`函数取代. 但是如果要在`then`函数里面, 做异常的处理, 第一个参数可以传`null`

```JavaScript
const p = new Promise((resolve, reject)=>{
	resolve(456)
	reject('456')
})
p.then(res=>{
		console.log(res)
	},
	err=>{
		console.log(err)
})
```
* 创建已决的`Promise`
	* 已决的`promise`可以接收接收值,也可以接收`promise`
*   `Promise.resolve（）`：可以接受一个非`Promise`、非`thenable`的立即值，则返回用这个值填充的`promise`。如果传一个非`promise`的`thenable`值，就会试图展开这个值，而且展开过程会持续到提取出一个具体的非类`promise`的最终值。如果传入一个`promise`，就会返回同一个`promise`(可以是拒绝或者完成)。
*  `Promise.reject()`: 不会像`resolve`那样对传入的`thenable和promise`进行展开,而是把传入的值原封不动的设置为拒绝理由。后续的拒绝处理函数接收到的是你实际传给`reject（..）`的那个`promise|thenable`，而不是底层的立即值。
*  `Promise.all([..])`: 参数为一个数组(或者可迭代,里面的值可以是立即值|`thenable|promise`等。如果数组是空的，主`Promise`会立即完成),对于数组里面的值，会用`Promise.resolve()`进行处理。通常有`Promise`实例组成。从`Promise.all([..])`返回的`promise`会接收到一个消息。这是一个有所有传入`promise`的完成消息组成的数组，与制定的顺序一致（与完成顺序无关）。从`promsie.all([..])`返回的主promise在且仅在所有的成员`promsie`完成后才会完成。如果有任何一个被拒绝的话，主`Promise.all[..]`就会立即被拒绝,并丢弃来自其他所有`promise`的全部结果。
*  `Promise.race([..])`: 参数和上面的一样。如果传入的数组是空的,则会一直没有决议,所以不要传空数组。参数里面有一个完成，则立即完成。有一个拒绝，则立即拒绝（参数里面的`promsie`是竟态的）
* `Promise.finally(onFinallyFn)`: 类似于`ajax`的`finally`, 执行完`then()和catch()`后, 都会执行`finally`指定的回调函数, 避免同样的语句需要再`then()和catch()`中各写一次的情况
* `Promise`链中默认返回一个`Promise`, 如果有返回值, 则这个值作为返回的`Promise`的`value`,可以再下一个`Promise`链中可以获取这个返回值.也可以在`Promise`链中返回一个`Promise`来取代默认的`Promise`
	
```JavaScript
//创建决议成功的
 Promise.resolve(v)
	.then((res)=>{
		//...
	})
// 创建决议失败的
	Promise.reject(err)
	.then(null,err=>{
		//	...
})
// 常见决议失败的
Promise.reject(err).catch(err=>{
// 	...
})

// thenable对象: 拥有then方法, 且该方法接受resolve,reject作为参数

let thenable = {
	then: (resolve, reject) =>{
		resolve(444)
	}
}
```

#### 可以继承`Promise`
* 可以通过继承`Promise`, 来实现自己的变异的派生类

### `async await function`

> `async function name([param[, param[, ... param]]]) { statements }`  

该函数返回一个`Promise对象(隐式转换)`, 返回的`Promise`对象会以`async function`的返回值进行解析，或者以该函数抛出的异常进行回绝。
当`async` 函数返回一个值时，`Promise `的 `resolve `方法会负责传递这个值，当 `async` 函数抛出异常时，`Promise `的 `reject `方法也会传递这个异常值。 
在`async `函数中如果遇见 `await `表达式，则` async `函数会暂停执行，等待表达式中的 `Promise` 解析完成后继续执行 `async` 函数并返回结果。

```javascript
function resolveAfter2Seconds() {
  return new Promise(resolve => {
    setTimeout(() => {
      resolve('resolved');
    }, 2000);
  });
}

async function asyncCall() {
  console.log('calling');
  var result = await resolveAfter2Seconds();
  console.log(result);
	return result
  // expected output: 'resolved'
}

asyncCall().then(res=>{
	console.log(res)	
},err=>{
	console.log(err)
})
```

### 代理和反射接口
> 语法: `let p = new Proxy(target, handler);`  
`target`:  用`Proxy`包装的目标对象（可以是任何类型的对象，包括原生数组，函数，甚至另一个代理）。
`handler`: 一个对象，其属性是当执行一个操作时定义代理的行为的函数。

> `Proxy.revocable(target, handler);`可以创建一个可以撤销的代理对象, 返回一个对象{proxy: proxyObj, revoke: revokeFn},   
`proxy`: 该属性的值为新生成的代理的对象本身
`revoke`: 撤销方法, 调用的时候不需要加任何参数, 就可以撤销掉和它一起生成的那个代理对象。代理撤销后, 代理对象在访问陷阱函数时会抛出错误
代理对象的行为会代理到目标对象上, 但是对目标对象没有影响

####  代理的陷阱函数图表, 代理陷阱和`js引擎`默认的行为

| 代理陷阱| 被重写的行为 | 默认行为 |
|  :------: |  :------:|  :------:|
|   get  |     读取一个属性的值                                 | Reflect.get() |
|  se t   |      写入一个属性                                       | Reflect.set()|
|  has  |      in运算符                                     | Reflect.has()|
|  deleteProperty   |      delete 运算符        | Reflect.deleteProperty()|
|  getPrototypeOf  |     Object.getProtorypeOf()  | Reflect.getProtorypeOf()|
|  setPrototypeOf  |      Object.setPrototypeOf ()   | Reflect.setPrototypeOf ()|
|  isExtensible   |    Object. isExtensible()                 | Reflect.isExtensible()|
|  preventExtensions   |   Object.preventExtensions()   | Reflect.preventExtensions()|
|  getOwnPropertyDesciptor   |  Object.getOwnPropertyDesciptor ()                  | Reflect.getOwnPropertyDesciptor ()|
|  defineProperty  |  Object.defineProperty()  | Reflect.defineProperty()|
| ownKeys  |     Object.keys Object.getOwnPropertyNames()与Object.getOwnPropertySymbols()               | Reflect. ownKeys()|
| apply  |      调用一个函数                  | Reflect.apply()|
| construct  |    使用new调用一个函数                  | Reflect. construct()|

#### `set`陷阱函数(访问器属性), 接受4个参数
* `trapTarget`: 将接收属性的对象(代理的目标对象)
* `key`: 需要写入的属性的键(字符串类型或符号类型)
* `value`: 将被写入属性的值
* `receiver`: 操作发生的对象(通常是代理对象)
* 对应的默认的行为`Reflect.set()`接收的参数和陷阱函数一致

```JavaScript
const handler = {
      set: function(target, key, value, receiver) {
        if(!target.hasOwnProperty(key)) {
          if(typeof value !== 'number'||isNaN(value)) {
            throw new Error('value must number')
          } 
          return Reflect.set(target, key, value, receiver)
        }
      }
    }
    const target = {
      name: 'wyt'
    }

    const p = new Proxy(target, handler)
    p.age = 18
    p.sex = 'wyt' // 报错
```

#### `get`陷阱函数(访问器属性), 接收三个参数
* `trapTarget`: 将会读取属性的对象(代理的目标对象)
* `key`: 需要读取属性的键(基本类型)
* `receiver`: 操作发生的对象(通常是代理对象)

```javascript
const handler = {
      get: function(target, key, receiver) {
        // 这里使用receiver(拥有has陷阱函数)
        if(!(key in receiver)){
          throw new Error('not match key')
        }
        return Reflect.get(target, key, receiver)
      }
    }
    const target = {
      name: 'wyt'
    }

    const p = new Proxy(target, handler)
    p.age = 18
    console.log(p.sex);
```


#### `has`陷阱函数(在使用`in`运算符被调用): 接收俩个参数
* `trapTarget`: 将会读取属性的对象(代理的目标对象)
* `key`: 需要读取属性的键(基本类型)

```JavaScript
const handler = {
      has: function(target, key) {
        if(target.hasOwnProperty(key)) {
          return true
        }
        return false
      }
    }
    const target = {
      name: 'wyt'
    }

    const p = new Proxy(target, handler)
    console.log('toString' in p)  // false
    console.log('name' in p);  // true
``` 

#### `deleteProperty`陷阱函数(在使用`delete`运算符调用): 接收俩个参数
* 对象的属性是否可以被删除,依赖其数据属性的`[[Configable]]`是否为`true`, 不过陷阱函数, 可以对为`true`的属性做一些操作
* `trapTarget`: 需要删除属性的对象(即代理的目标对象)
* `key`:  需要删除的属性的键(字符串类型或符号类型)

```JavaScript
const handler = {
      deleteProperty(target, key) {
        // 这里先假定vlaue属性不能被删除
        if(key === 'value') {
          return false
        }
        return Reflect.deleteProperty(target, key)
      }
    }
    const target = {
      name: 'wyt',
      age: 18, 
      value: 16
    }
   // Object.defineProperty(target,'value',{
   //   configurable: false
   // })
    const p = new Proxy(target, handler)
    console.log('value' in p);
    console.log(delete p.value);
    console.log('value' in p);
```

#### 原型代理的陷阱函数: `setPrototypeOf和getPrototypeOf`
* `setPrototypeOf`: 会对对象的`Object.setPrototypeOf(obj, proto)`进行拦截, 同样接收俩个参数
	* `trapTarget`: 需要设置原型的对象(即代理的目标对象)
	* `proto`: 需要被用作原型的对象
	* 返回一个布尔值, 表示设置是否成功
* `getPrototypeOf`: 会对对象的`Object.getPrototypeOf(obj)`的行为进行拦截, 接收一个参数
	* `trapTarget`: 需要获取原型的对象(即代理的目标对象)
* 上面的陷阱函数, 执行的默认行为是`Reflect.setPrototype和Reflect.getPrototypeOf()`, 这些函数和`Object`原型上的函数还是有差异的
	* `Object.setPrototypeOf(obj, proto)和Reflect.setPrototypeOf`: 
		*  第二个参数都要求是`null || object`传入其他参数会报错
		* 前者操作会返回`obj`
		* 后者返回布尔值. 来表示设置是否成功
	* `Object.getPrototypeOf(obj)和Reflect.getPrototypeOf`:
		* 前者会在操作之前先将参数值转换为一个对象(隐式转换,基本类型转换为对应的包装类型,  但是必须是有效值 `null和undefined会报错`)
		* 而后者要求参数必须是对象, 其它参数会报错

```JavaScript
const handler = {
      setPrototypeOf(target, proto) {
        return Reflect.setPrototypeOf(target, proto)
      },
      getPrototypeOf(target) {
       return Reflect.getPrototypeOf(target)
      }
    }
    const target = {
      name: 'wyt',
      age: 18, 
      value: 16
    }
    const p = new Proxy(target, handler)
    const proto = Object.setPrototypeOf(p, null)
    const _proto = Object.setPrototypeOf(target, null)
    console.log(proto, _proto);

    const pt = Reflect.getPrototypeOf(null) // error
    const _pt = Object.getPrototypeOf(22) // 返回对应的包装类型 
	
```

#### 可扩展的陷阱函数:  `preventExtensions 和 isExtesible`

* `preventExtensions`:  是对`Object.preventExtensions()`做的拦截, 阻止对象可扩展
* `isExtensible`: 是对`Object.isExtensible()`检测对象是否可扩展
	* 上面俩个方法都只接收一个参数`trapTarget`, 需要设置或要获取是否可扩展的代理对象
	* 一旦禁止对象扩展, 则对象不能添加其他的属性类似的方法还有
		* `Object.preventExtensions(o)`: 阻止对象扩展, 不能再添加新的属性, 但是可以操作已有属性(删除,枚举,可写等)
		* `Obejct.isExtensible(o)`:  检测对象是否可扩展
		* `Object.freeze(o)`:这个方法比 Object.seal 更绝，冻结对象是指那些不能添加新的属性，不能修改已有属性的值，不能删除已有属性，以及不能修改已有属性的可枚举性、可配置性、可写性的对象。也就是说，这个对象永远是不可变的。
		* `Object.seal(o)`: 让一个对象密封，并返回被密封后的对象。密封对象是指那些不能添加新的属性，不能删除已有属性，以及不能修改已有属性的可枚举性、可配置性、可写性，但可以修改已有属性的值的对象
		* `Object.isFrozen(o)`: 检测一个对象是否被冻结
		* `Obejct.isSealed(o)`:  检测一个对象是否被密封
```JavaScript
const handler = {
	preventExtensions(target) {
		return Reflect.preventExtensions(target)
	},
	isExtensible(target) {
		return Reflect.isExtensible(target)
	}
}

const target = {
	name: 'wyt',
	age: 12
}

   const p = new Proxy(target, handler)

    console.log(Object.isExtensible(p));
    console.log(Object.isExtensible(target));
    console.log(Object.preventExtensions(p));
    console.log(Object.isExtensible(p));
    console.log(Object.isExtensible(target));
```

#### 属性描述符的陷阱函数: `defineProperty 和 getOwnPropertyDescriptor`
* `defineProperty`是对`Object.defineProperty()`进行拦截, 接收三个参数
	* `target`: 需要被定义属性的对象(即代理的目标对象)
	* `key`: 属性的键(字符串类型或者符号类型)
	* `descriptor`: 该属性准备的描述符对象
* `getOwnPropertyDescriptor`: 是对`Object.getOwnPropertyDescriptor()`进行拦截的, 接收俩个参数
	* `target`: 需要获取属性的对象(即代理的目标对象)
	* `key`: 属性的键(字符串类型或者符号类型)

```JavaScript
const handler = {
// 这里可以拦截我们不想被修改的属性, 只需要返回false(报错)就可以
	 defineProperty(target, key, descriptor) {
        // 非标准属性无效
        descriptor = Object.assign({}, descriptor, {
          name: 'key',
          age: '22'
        })
        return Reflect.defineProperty(target, key, descriptor)
        // return false
      },
      getOwnPropertyDescriptor(target, key) {
        // 如果返回的是对象,对象的属性必须是标准属性, 否则报错
        return Reflect.getOwnPropertyDescriptor(target, key)
        // return {
        //   value: target[key],
        //   configurable: false,
        //   writable: false, 
        //   enumerable: false
        // }      }
    }
    const target = {
      name: 'wyt',
      age: 18, 
      value: 16
    }

    const p = new Proxy(target, handler)

    Object.defineProperty(p, 'sex', {
      configurable: false,
      writable: false,
      enumerable: false,
      value: 'male'
    })
```

* 描述符对象的限制:  
	* 对象的自有属性: `enumerable,configurable,value, writable, get, set`
	* `Object.defineProperty(o,k,d)`: 第三个参数: 可以是任意的对象, 但是只有对象的自由属性是被许可的(`Reflect.defineProperty`同样也会忽略非标准属性)
	* `getOwnPropertyDescriptor`:  的返回值必须是`null,undefined,对象`, 如果是一个对象, 则对象的属性也必须是上面的自有属性, 有其它非标准属性时会报错
* 方法的差异
	* `Object.defineProperty和Reflect.defineProperty`
		* 前者返回第一个参数
		* 后者操作成功返回`true,` 否则返回`false`
	* `Object.getOwnPropertyDescriptor和Object.getOwnPropertyDescruptor`
		* 前者第一个参数是基本类型值时, 会隐式转换为对应的包装类型
		* 后者第一个参数是基本类型的时候, 会报错
		
#### `ownKeys`陷阱函数
* 该代理陷阱拦截了内部方法`[[OwnPropertykeys]]`, 并允许你返回一个数组用于重写该行为. 返回的这个数组会被用于四个方法: `Object.keys,Object.getOwnPropertyNames,Object.getOwnPropertySymbols, Object.assign`. 其中`Object.assign()`方法会使用该数组来决定哪些属性会被复制
* 只接收一个参数: `target`:  需要获取的对象(即代理对象)
```JavaScript
const handler = {
ownKeys(target) {
        // 这里假定要求key如果是字符串, 则不能下划线开头
        return Reflect.ownKeys(target).filter((k,i,c) => {
          return typeof k !== 'string' || k[0] !== '_'
        })
      }
    }
    const target = {
      name: 'wyt',
      age: 18,
      value: 16
    }
    const KEY = Symbol('name')
    const p = new Proxy(target, handler)
    p['_name'] = 'hhh'
    p[KEY] = 'gg'

    console.log(Object.keys(p));
    console.log(Object.getOwnPropertyNames(p));
    console.log(Object.getOwnPropertySymbols(p));
    console.log(Object.assign({}, p, {
      '_tt': 11
    }));
```

#### 使用`apply`与`construct`陷阱函数的函数代理: 要求代理对象必须是一个函数, 函数拥有俩个内部方法: `[[Call]]与[[Construct]]`, 前者会在函数被调用的时候执行 , 后者会在函数被`new`运算符调用时执行
* `apply`: 陷阱函数, 会在函数被调用的时候拦截, 接收三个参数
	* `trapTarget`:被执行的函数(即代理的目标对象)
	* `thisArg`: 调用过程中内部的`this`值
	* `argumentsList`: 被传递函数的参数数组
* `construct`:  会在函数通过`new`关键字调用时进行拦截, 接收俩个参数
	* `trapTarget`: 被执行的函数(即代理的目标对象)
	* `argumentsList`: 被传递给函数的参数数组
* 这俩个陷阱函数可以结合起来可以让我们控制函数的调用, 例如参数检测

```JavaScript
const handler = {
	apply(target, context, args) {
        console.log(context);
        return Reflect.apply(target, context, args)
      },
      construct(target, args) {
        return Reflect.construct(target, args)
      }
    }
    let target = function(name) {
      this.name = name
      return 66
    }
    const p = new Proxy(target, handler)
    console.log(typeof p === 'function');

    console.log(p());
    const instance = new p('name')
    console.log(instance instanceof p);
```

* 进行参数校验
```JavaScript
const handler = {
	apply(target, context, args) {
        for (const k of args) {
          if (typeof k !== 'number') {
            throw new Error('parameter must be number')
          }
        }
        return Reflect.apply(target, context, args)
      },
      construct(target, args) {
        // return Reflect.construct(target, args)
        throw new Error('this function can`t be called by new')
      }
    }
    let target = function(...args) {
      return args.reduce((prev,current)=> prev + current,0)
    }
    const p = new Proxy(target, handler)
    console.log(typeof p === 'function');

    console.log(p(1,2,3,4));
    const instance = new p('name')
```

* 可以用上面的陷阱函数重写基础类的构造器, 比如类必须要求`new`关键字调用, 或者有些函数构造器调用,但是有不想`new.target`是该构造函数, 可以借助陷阱函数`apply`来实现

```JavaScript
class Person{
	constructor(name) {
		this.name = name
	}
}

const p = new Proxy(Person, {
	apply(target, this, args) {
		return new Target(args)
	}
})

console.log(p('wyt'))
```

#### 可撤销的代理

>  可以使用`Proxy.revocable()`创建可以撤销的代理,返回值是一个对象  

* `proxy`:可被撤销的代理对象
* `revoke`: 需要代理的`handler`(代理的陷阱函数)
* 当调用`revoke`函数后,不能通过`proxy`执行进一步的操作,任何与代理对象交互的尝试都会触发代理陷阱抛出错误

```JavaScript
 const handler = {
      get(target, key) {
        return Reflect.get(target, key)
      }
    }
    const target = {
      name: 'wyt'
    }
    const {
      proxy,
      revoke
    } = Proxy.revocable(target, handler)

    console.log(proxy.name)   // wyt
    console.log(revoke());    // 
    console.log(proxy.name)   //报错
```

#### 代理陷阱函数基本是以下这么多, 不顾代理对象可以是普通的对象,也可以是特殊的, 比如原型

```javascript
const handler = {
      set(target, key, value, receiver) {
        if (!target.hasOwnProperty(key)) {
          return Reflect.set(target, key, value, receiver)
        }
      },
      get(target, key, receiver) {
        // 这里使用receiver(拥有has陷阱函数)
        if (!(key in receiver)) {
          throw new Error('not match key')
        }
        return Reflect.get(target, key, receiver)
      },
      has(target, key) {
        if (target.hasOwnProperty(key)) {
          return true
        }
        return false
      },
      deleteProperty(target, key) {
        // 这里先假定vlaue属性不能被删除
        if (key === 'value') {
          return false
        }
        return Reflect.deleteProperty(target, key)
      },
      setPrototypeOf(target, proto) {
        return Reflect.setPrototypeOf(target, proto)
      },
      getPrototypeOf(target) {
        return Reflect.getPrototypeOf(target)
      },
      preventExtensions(target) {
        return Reflect.preventExtensions(target)
      },
      isExtensible(target) {
        return Reflect.isExtensible(target)
      },
      defineProperty(target, key, descriptor) {
        // 非标准属性无效
        descriptor = Object.assign({}, descriptor, {
          name: 'key',
          age: '22'
        })
        return Reflect.defineProperty(target, key, descriptor)
        // return false
      },
      getOwnPropertyDescriptor(target, key) {
        // 如果返回的是对象,对象的属性必须是标准属性, 否则报错
        return Reflect.getOwnPropertyDescriptor(target, key)
        // return {
        //   value: target[key],
        //   configurable: false,
        //   writable: false, 
        //   enumerable: false
        // }
      },
      ownKeys(target) {
        // 这里假定要求key如果是字符转则不能下划线开头 
        return Reflect.ownKeys(target).filter((k,i,c) => {
          return typeof k !== 'string' || k[0] !== '_'
        })
      },
      // apply construct 要求代理对象是一个函数
      apply(target, context, args) {
        for (const k of args) {
          if (typeof k !== 'number') {
            throw new Error('parameter must be number')
          }
        }
        return Reflect.apply(target, context, args)
      },
      construct(target, args) {
        // return Reflect.construct(target, args)
        throw new Error('this function can`t be called by new')
      }
    }
    let target = function(...args) {
      return args.reduce((prev,current)=> prev + current,0)
    }
    // const target = {
    //   name: 'wyt',
    //   age: 18
    // }
    const p = new Proxy(target, handler)
```

#### 将代理用作原型

* 在原型上使用`get`陷阱

	* 只有在目标对象没有属性时, 才回去原型上查找, 才会调用原型的`get`方法 
```javascript
const target = {}
const thing = Object.create(new Proxy(target,{
      get(target, key, receiver) {
  		throw new Error('can`t visit prototype')
      }
  }))
  
  thing.name = 'wyt'
  console.logI(thing.unknown)
```

* 再原型上使用`set`陷阱函数
内部方法`[[Set]]`同样会查找对象自身的属性,并在必要时在对象的原型上查找.当对一个对象赋值时, 如果指定属性在该对象上已存在, 则直接赋值, 否则会查找对像的原型. 但是有趣的是,原型上是否有该属性, 都不影响再该对象上新建该属性

```JavaScript
const handler = {
	set(t, k, v, r) {
		console.log('set visit this prototype')
		return Reflect.set(t, k, v, r)
	}
}

const target = {
	name: 'wyt'
}

const p = Object.create(new Proxy(target, handler))

p.name = 'lfl' // 不触发
p.age = 12    // 触发
```

* 在原型上使用`has`陷阱函数

和`set以及get`比较像, 都是在对象自身没有该属性时, 再回去对象的原型上查找, 才会触发对应的陷阱函数

```javascript
const handler = {
	has(t, k) {
		console.log('has visit this prototype')
		return Reflect.has(t, k)
	}
}

const target = {name: 'wyt'}
const p = Object.create(new Proxy(target, handler))
p.age = 12
console.log('age' in p) // 不触发
console.log('name' in p) // 触发
```

#### 将代理作为类的原型
类不能直接修改为将代理作为自己的原型对象, 因为他们的`ptototype`属性是不可写入的. 但是可以使用继承来实现

```javascript
const handler = {
      get(t,k,r) {
        console.log('get visit this prototype');
        // return Reflect.get(t,k,r)
        throw new Error('get visit this prototype')
      }
    }
    const target = {
      name: 'wyt'
    }
    function NoSuchProperty() {}
    NoSuchProperty.prototype = new Proxy(target, handler)

    class Square extends NoSuchProperty{
      constructor(l,w) {
        super()
        this.length = l
        this.width = w
      }

      getArea() {
        return this.width * this.length
      }
    }

    const s = new Square(6,2)
    console.log(s.getArea());
    console.log(s.length * s.widt);
```

### 用模块封装代码
1. 模块代码自动运行在严格模式下,并且没有任何办法跳出严格模式
2. 在模块的顶级作用域穿件的变量, 不会添加到共享的全局作用域, 它们只会在模块顶级作用域的内部存在
3. 模块的顶级作用域的`this`值为`undefined`
4. 模块不允许在代码中使用`HTML`风格的注释
5. 对于需要让模块外部代码访问的内容, ,模块必须导出他们
6. 允许模块从其它模块导入绑定
7. 模块语法的限制: `export 与 import`都有一个重要的限制, 那就是他们必须被用在其它语句或表达式的外部

#### 暴露模块: 通过关键字`export`
* 可以通过关键字将一个模块内的`声明||方法||类`等暴露出去

```javascript
export function getName() { 
  console.log('wyt');
}

export class Person { 
  constructor(name) {
    this.name = name
  }

  getName() { 
    console.log(this.name);
  }
}
function serializeObj(obj) { 
  let ret = ''
  for (const k in obj) {
    if (obj.hasOwnProperty(k)) { 
      ret += '&'+k +'='+obj[k]
    }  
  }
  return ret.slice(1)
}
function getQueryName(name) { 
  const search = location.search
  const ret = search.match(new RegExp('[?&]'+name+'=([^&]*)'))
  return ret&&ret.length >0 ?ret[1] : ''
}

export { getQueryName}
```
#### 导入模块: 通过关键字 `import`
* 可以通过该关键字导入一个模块的一部分: `import {getQueryName, Person} from 'filePath'`
* 也可以导入一个模块的全部: `import * as moduleName from 'filePath'`
* 修改导入的名称 `import { xx as jj}  from 'filePath'`
* 对与一个模块多次被导入, 但是只会执行一次.(导出的模块的代码执行完, 就被放在内存中, 并随时被其它`import`所引用)

```javascript
import { getQueryName, Person as P } from './js/test'

const href = location.href
const name = getQueryName('name')
if (!name) {
  location.href = href + '?name=wyt&age=12'
}
const p = new P(name)

p.getName()
```

#### 导入绑定的一个微妙怪异点
* `ES6`的`import`语句为变量,函数与类创建了只读绑定, 而不像普通变量引用了原始绑定. 尽管导入绑定的模块无法修改绑定的值, 但负责导出的模块却能做到这一点

```javascript
export let name = 'wyt'

export function setName(newName) {
	name = newName
}
```

#### 导出默认值和导入默认值
* 导出默认值使用的`default`关键字, 但是一个模块只能有一个默认导出
```javascript
export default function(a, b) {
	return a + b
}

// 2

function sum(a, b) {
	return a + b 
}
export default sum

// 也可以这样导出
export {sum as default}
```

* 导入默认值, 从一个模块导入默认值, 不需要花括号`import xx from filePath`
	* 也可以同时从一个模块导入默认,和非默认

```javascript
import sum from 'filePath'

// 同时导入非默认模块

import sum, {getName} from 'filePath'

// 上面的等价

import {default as sum , getName}  from 'filePath'
```

#### 绑定的再导出
* 有时从一个模块导入的方法或者类,我们在对其扩展完, 可以再导出

```javascript

import {sum} from 'filePath'

sum.extend = {...}
export {sum}

// 如果不做任何操作, 可以简化导出的语句
export {sum, getName as gerPropertyName} from 'filePath'

// 亦或者导出全部
export * from 'filePath'
```

#### 路径解析: 
* `/file/...`: 绝对路径, 会从跟目录开始解析
* `./file...`: 相对路径, 会从当前目录开始解析


## EC7 新增的特性
#### 幂运算符: `n1**n2`
* 左侧是底数, 右侧是指数, `EC5`中幂的运算是`Math.pow(n1,n2)`
```javascript
Math.pow(5,2) === 5**2
```

#### 新增的数组方法
* `Array.prototype.includes(v, startIndex)`: 用于检车某个字符串是否存在于指定字符串中, 返回布尔值. 
* 该方法对于值的比较用的是`===`, 但是对于`NaN`该方法认为是相等的
* `EC5`中有返回对应值的索引的方法: `Array.prototype.indexOf(v, startIndex)`如果有值返回对应值的索引, 没有返回`-1`, 但是这个方法做比较时, 认为`NaN`是不相等的

```javascript

const name = 'qye'
const arr = [name, 1, 'age', NaN, +0]

arr.includes(name)  // true
arr.includes(NaN)   // true
arr.includes(-0)    // true
```




		

#mydiary/javascript/base