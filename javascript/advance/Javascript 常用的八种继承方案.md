# Javascript 常用的八种继承方案
### 原型链继承
* 缺点: 多个实例对引用类型的操作会被篡改

```javascript
function SuperType() {
    this.property = true;
} 
SuperType.prototype.getSuperValue = function() {
    return this.property;
}
function SubType() {
    this.subproperty = false;
}

/// 这里是关键，创建SuperType的实例，并将该实例赋值给SubType.prototype/
SubType.prototype = new SuperType(); 
SubType.prototype.getSubValue = function() {
    return this.subproperty;
}
var instance = new SubType();
console.log(instance.getSuperValue()); /// true/
```

### 借用构造函数继承
* 使用父类的构造函数来增强子类实例i,等同于复制父类的实例给子类
* 缺点:
	* 只能继承父类的实例属性和方法,不能继承原型属性/方法
	* 无法实现复用,每个子类都有父类实例函数的副本,影响性能
```javascript
function Super() {
	this.colors = ['red','green']
}
function sub() {
	Super.call(this)
}

const instance_1 = new Sub()
instance_1.colors.push('black')

const instance_2 = new Sub()
console.log(instance_2.colors)
```

### 组合继承
* 缺点:
	*  需要调用俩次父类
	* 子类的实例的属性回合原型上的属性重复
```javascript
function Super() {
	this.name = name
	this.colors = ['red','blue']
}
Super.prototype.sayName = function() {
	console.log(this.name)
}

function Sub(name,age) {
	Super.call(this,name)
	this.age = age
}

Sub.prototype = new Super()
Sub.prototype.constructor = Super

const instance = new Sub('wyt',27)
console.log(instance)
```

### 原型式继承
* 利用一个空对象作为中介,将某个对象直接赋值给空对象构造函数的原型
* 也是`Object.create`的模拟实现
* 缺点: 
	* 原型链继承多个实例的引用类型属性指向相同,存在篡改的可能
	* 无法传递参数
```javascript

function object(obj) {
	function F() {}
	F.prototype = obj
	return new F()
}
```

### 寄生式继承
* 主要用来增强原型的属性, 无法传递参数
```javascript
function create(obj) {
	const newObj = Object.create(obj)
	newObj.sayHi = function() {
		console.log('hello, body!')
	}
	return newObj
}

const o = {
	getName() {
		console.log('my name is wyt')
	}
}

const s = create(o)
s.sayHi()
```

### 寄生组合继承

* 前面的组合继承会调用俩次父类的构造函数

```javascript

function create(obj) {
	function F() {}
	F.prototype = obj
	return new F()
}

function inheritPrototype(sub, super) {
	const prototype = create(super.prototype)
	prototype.constructor = super
	sub.prototype = prototype
}

function Super(name){
	this.name = name
}
Super.prototype.getName = function() {
	console.log(this.name)
}

function Sub(name,age) {
	Super.call(this,name)
	this.age = age
}

inheritPrototype(Sub, Super)
```

### 类继承
* 类继承是函数继承的语法糖, 但是不能继承属性,只能继承方法,属性和方法重名时会被覆盖

#mydiary/javascript/advance