# typescript
#### `Interface`接口的一些特性
* 索引签名: 包括数字索引签名和字符串索引签名
	* 当俩种签名都存在的时候, 数字索引签名的返回值,必须是字符选索引签名返回值的子类型
```javascript
class Animal{
	name: string
}
class Dog extends Animal {
	breed: string
}

// 这是ok的, Dog和Animal交换位置会报 
interface Test {
	[x: number]: Dog
	[x: string]: Animal
}



```

* 额外的属性检查: 
	* 使用类型断言
	* 使用索引签名
	* 引入第三变量
* 描述函数类型
	* 需要使用调用签名

```javascript
interface SearchFun {
	(name: string, age: number):void
}

const myFn:SearchFun

function myFn(name: string, age: number):void {
	console.log(`my name is ${name}, my age is ${age}`)
}
```

#### 类属性的几个类型的区别
* `private`:   该类型的属性, 只有在其声明的类中, 才可以访问到
* `protected`: 声明的类中和子类可以访问到. 类的外面访问不到
* `public`: 声明的类中和子类, 以及外部都可以访问到

#### `.gitignore`文件

```javascript
# See https://help.github.com/articles/ignoring-files/ for more about ignoring files.

# dependencies
/node_modules
/.pnp
.pnp.js

# testing
/coverage

# next.js
/.next/
/out/

# production
/build

# misc
.DS_Store
.env*

# debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*

```
#mydiary/typescript/basic#