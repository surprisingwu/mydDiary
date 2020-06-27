# typescript的一些基础知识
#### 基础类型
* 一些基础类型的声明

```js
// number 类型
const a:number = 12

// string 类型
cosnt a: string = 'name'

// boolean 类型
const a:boolean = true

// null && undefined
const a : null = null
const a : undefined = undefined

// never 类型, 用于达不到的边界
function throwError(err: string):never{
	throw new Error(err)
}

// void 类型
const a: void = undefined

// 枚举类型
enum Status{
	OFFLINE,
	ONLINE,
	OTHER
}
const statusValue = Status[OFFLINE]  // 0

// 元组. 知道数组的长度, 和值的类型
tuple TestArray:[number, string] = [1, 'name']
 

// any 任意类型
const a:any = 123
a.Tostring()
a.name   // 都不会报错

// 类型断言
interface Person{
	name: string
	age: number
}
const t:any = {
	name: 'wyt',
	sex: 1
} 

// t as Person   || <Person>t
const s = (t as Person).name

// 数组 俩种方式
const array: number[] = [1,2 ,3]
const arrayTest: Array<string> = ['1', '2']
```
#### 接口
* 除了基础类型, 几乎所有的对象类型, 都可以用接口来声明
```js
// 普通的接口类型
interface Person {
	name: string
	age: number
}
function f1({name, age}: Person) {
	console.log(`My name is ${name}, age is ${age}`)
}
// 可选属性
 interface Person {
	name?: string
	age: number
}
const a: Person = {
	age: 12
}
// 只读属性
interface Person {
	readonly name: string
}
cosnt b:Person = {
	name: 'wyt'
}
b.name = 'lfl'  // 报错
// 接口声明函数  调用签名
interface Fn {
	({ name: string, age: number }
): any
}
const f2: Fn = f1
// 混合类型
interface Fn {
	(name: string, age: number): void
	version: string
}
const f1: Fn = (name, age) => {
	console.log(nane, age)
} 
f1.version = 'v1.1'
// 构造签名 不能直接 new
interface Person {
	new (name: string, age:number)
}

function createInstance(fn: Person):Person {
	return new fn('wyt', 18)
}
// 索引签名: 字符串索引签名  数字索引签名. 但是数字索引签名是字符串索引签名的子类型
interface Person {
	[propName: string]: string
	[index: number]: string
}
const p:Person = {
	name: '1',
	1: '2'
}
// 接口继承接口
interface PersonName {
	name: string
}

interface PersonAge {
	age: number
}

interface Person extends PersonName,PersonAge {
	sex: 2
}

const a:Person = {
	name: 'wyt',
	age: 18,
	sex: 1 
} 
// 接口继承类
class Super{
    constructor(public sex: number) {}
}

interface P1 extends Super {
    getName():void
}

class P2 implements P1 {
    constructor(public sex: number) { }
    getName() {
        console.log(this.sex)
    }

}


// 类实现接口
interface Person {
	name: string
	age: number
}

class P implement Person {
	constructor(public name: stirng, public age: number){}
}

// 相同的接口,会合并
interface User {
	name: string
	age: 12
}
interface User {
	sex: 1
}
// 上面的俩个接口会合并, 等价于

interface User {
	name: string
	age: number
	sex: 1
}
```

#### `type`(类型别名) 和 `interface`(接口)的区别

* `interface`: 只能定义对象类型, 可以继承和被类实现. 多个重名的接口,会合并
* `type`:  类型别名会给一个类型起个新名字.类型别名有时和接口很像, 但是可以作用于原始值,联合类型,元组以及其它你需要手写的类型.
* `type` 不能`extend`和`implement`. 多个`type`重名时,会报错.

*  相同点 :都可以描述一个对象或者函数

```js
// *interface*
interface User {
  name: string
  age: number
}

interface SetUser {
  (name: string, age: number): void;
}
// *type*
type User = {
  name: string
  age: number
};

type SetUser = (name: string, age: number)=> void;
```

* 都允许拓展
	* `interface` 和 `type` 都可以拓展，并且两者并不是相互独立的，也就是说 `interface` 可以 `extends type`, `type` 也可以 `extends interface` 。 **虽然效果差不多，但是两者语法不同**。

```js
// *interface extends interface*
interface Name { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}

// *type extends type*  type的继承和interface的继承不同
type Name = { 
  name: string; 
}
type User = Name & { age: number  };
// *interface extends type*
type Name = { 
  name: string; 
}
interface User extends Name { 
  age: number; 
}

// *type extends interface*
interface Name { 
  name: string; 
}
type User = Name & { 
  age: number; 
}
```

* 不同点
	* `type` 可以而 `interface` 不行
		* `type`可以声明基本类型别名，联合类型，元组等类型
```js
// 基本类型别名
type Name = string

// 联合类型
interface Dog {
    wong();
}
interface Cat {
    miao();
}
type Pet = Dog | Cat
// 具体定义数组每个位置的类型
type PetList = [Dog, Pet]

// type 语句中还可以使用 typeof 获取实例的 类型进行赋值
// 当你想获取一个变量的类型时，使用 typeof
let div = document.createElement(‘div’);
type B = typeof div

// 其他骚操作
type StringOrNumber = string | number;  
type Text = string | { text: string };  
type NameLookup = Dictionary<string, Person>;  
type Callback<T> = (data: T) => void;  
type Pair<T> = [T, T];  
type Coordinates = Pair<number>;  
type Tree<T> = T | { left: Tree<T>, right: Tree<T> };
```
		* `interface` 可以而 `type `不行,  
			* 类型别名不能`extends`和`implements`其它类型
```js
// interface 能够声明合并
interface User {
  name: string
  age: number
}
interface User {
  sex: string
}
/*
User 接口为 {
  name: string
  age: number
  sex: string 
}
*/
```

#### 类
* 类中声明变量的三种类型
	* `public, private, protected`

```js
// public: 默认类中声明的变量和方法都是public的. public的变量再类,子类和外部都是可以使用的
class Person {
    constructor(public name:string, private age:number, protected sex: number) {}
    getName() {
        console.log(`My name is ${this.name}, my age is ${this.age}`)
    }
}

class Man extends Person {
   interest: string
   constructor(name, age, sex, interest: string) {
        super(name, age, sex)
        this.interest = interest
    }
   greet() {
        console.log(`My interest is ${this.interest}, 性别是${this.sex}`)
    }
		// 会报错
	getAge() {
		return this.age
	}
}

const p = new Man('wyt', 18, 1, '足球')

console.log(p.age) // 报错

```
#### 函数
* 

#### 联合类型和类型保护
* 类型保护的方式: 

```js
// 使用类型断言
// 使用 in
// 使用 typeOf
// 使用 instanceOf

```
#### 泛型
* 泛指， 一类的类型
```js
interface Fn<T> {
	<T>(a:T):T
}

const fn:Fn<string> = function(a){
	 return a
}

fn(1) // 报错
fn('name')
```
* 泛型类型
* 泛型约束
* 泛型类
#### 泛型中 `keyOf`的使用
#### 命名空间
* `namepace import export`
#### `xx.d.ts`的文件的声明
* 可以配置全局的类型声明
#### 装饰器
* 类装饰器
* 方法装饰器
* 访问器装饰器
* 属性装饰器
* 参数装饰器

#mydiary/typescript