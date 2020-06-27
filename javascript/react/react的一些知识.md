#  react的一些知识
## React Hooks
#### 使用`Hook`的动机
* `Hook`是一些可以让你再函数组件里**钩入**`React state`及生命周期特性的函数. `Hook`不能再`class`组件中使用
* `Hook`的一些特性
	* `Hook`使你在无需修改组件结构的情况下复用状态逻辑
	* `Hook`将组件中相互关联的部分拆分成更小的函数(比如设置订阅或请求数据)
	* `Hook`让我们可以在函数组件里,使用只有`chass`组件才有的特性
#### `Hook`使用规则
* 只能在`函数最外层`调用`Hook`. 不要在循环,条件判断或者子函数中调用
* 只能在`React`的函数组件中调用`Hook`或者在自定义`Hook`中调用其他`Hook`. 不要在其他`JavaScript`函数抓中调用
	* 在使用`hook`的时候, 必须保证`hook`的顺序. 上面的一些规则就是为了保证`hook`的执行顺序

#### 一些`Hook api`
* `useState`: `const [state, setCount] = useState(initialState);`
	* `userState:`初始值需要复杂计算获得时,也可以传入一个函数
	* `setCount`:  如果新的`state`需要通过使用先前的`state`计算得出, 那么可以将函数传递给`setState.`该函数将接收先前的`state`,并返回一个更新后的值
* `useEffect` : 是`componentDidMount,componentDisUpdate,componentWillUnmount`的结合体
* `userLayoutEffect`: 其函数签名与`useEffect`相同, 但它会在所有的`DOM`变更之后同步调用`effect`
* `useContext`:
* `useReducer` 
```javascript
	function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
    </>
  );
}

}
```

* `useCallback`: 把内联回调函数以及依赖项数组作为参数传入`useCallback`,它将返回该回调函数的`memoized`版本

```javascript
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
// useCallback(fn, deps) 相当于  useMemo(() => fn, deps)
```

* `useMemo`:

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

* `useRef`:

```javascript
// const refContainer = useRef(initialValue);
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}


```

* `useImperativeHandle`: 
	* `userImpetativeHandle(ref, createHandle, [deps])`

## `Redux` 中的`state`的设计原则
* 管理被多个组件所依赖或者影响的状态. `Redux`的设计尽量范式化,扁平化
	* 范式化: 像设计关系数据库一样,减少冗余数据
	* 扁平化: `state`对象的数据结构不要太深.
* `Redux`设计的三大原则
	* `store`是唯一数据源
	* `state`只读(通过`reducer`根据上一个`state`返回一个新的`state`)
	* `reducer`是一个纯函数
*  数据按照领域把整个应用的状态按照领域(`Domain`)分成若干子`State,` 子`State`之间不能保存重复的数据.
*  表中的`State`以键值对的解构存储数据,以记录的`key/Id`作为记录的索引,记录中的其他字段都依赖于索引
*  `State`中不能保存可以通过已有数据计算而来的数据,即State中的字段不互相依赖
*  `state`尽量扁平化,避免嵌套过深
*  `UI State`: 具有松散型特点
## `Redux`的项目结构
*  按照类型划分
*  按照功能划分
*  `Ducks`
## ` Store Enhancer`和`Middleware`的关系
* `middleware`是`store enhancer`的一种
*  再日常的开发中,我们应该用`middleware`(比较高的抽象),而不是`store enhancer`(比较低的抽象)
## `MPA和SPA`的优缺点
* ` MPA`: 多页面应用. 不同的url返回的不同的页面, 有利于爬虫和`seo`.  更多的依赖于服务端的路由机制
* `SPA`: 单页面应用. 不利于`seo`, 用户体验好. 共用资源不会重新加载.性能比较好. 依赖的是客户端的路由.

## `React Router 4 ` 全新思维
> 包括`react-router,react-router-dom,react-touter-native`三部分  
* 使用路由的时候,必须要要用`<Router></Router>`包裹下. 而`Router`有俩种实现方式: `BrowserRouter和hashRouter`
	* ` <BrowserRouter></BrowserRouter>`
		* 是根据`HTML5 history API`: `pushState,replaceState`等实现
		的
		* 需要`web`服务器进行额外的配置,来支持单页面的`url`加载
	* `<HashRouter></HashRouter>`: 使用`url的hash`部分作为路由信息
		* 主要是为了 兼容老版本浏览器
* 路由匹配: 只要满足了`path`的匹配规则,就会显示出来
	* `exact`:  完全匹配时,才会显示匹配的路由
	* `<Switch/>`:  前面匹配到时,不会再匹配后面的路由
## `Route`渲染组件的方式
* `Route component`
* `Route render`
* `Route children`

```javascript
<Route path="/" exact component={Home} />

// 接受一个函数, 可以传递组件一些属性, match,history,location等
<Route path="/about" exact render = {(props) => <About {...props} {...extraProps}/>}

// props 中携带 match属性
<Route path="contact" children={props => props.match?'active':'inactive'} />
```

#### hook函数的优势

* 方便复用状态逻辑
* 副作用的关注点分离
* 解决`this`的指向问题

#### hook函数的使用规则
* 只能能函数组件里使用
* 只能再函数顶层使用
	* 不能再条件语句,循环语句和嵌套函数中使用

#### hooks常见问题

* [生命周期图解]([React lifecycle methods diagram](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/))


#### redux
* 三大原则: 
	* 单一数据源, 
	* `state`状态不可变
	*  纯函数修改状态
## 架构
*  抽象, 解耦,  组合
* 架构 => 框架设计模式 => 代码
* 前端架构的特点
	* 前端不是一个独立的子系统,又横跨整个系统
	* 分散性: 前端工程化
		* 可控: 脚手架,开发规范等约束
		* 高效: 框架,组件库,`Mock`平台,构建部署工具等
	* 页面的抽象, 解耦, 组合
		* 抽象:  
			* 页面`UI`抽象: 组件
			* 通用逻辑抽象: 领域实体,网络请求,异常处理等
	* 技术选型考虑的三要素
		* 业务满足程度
		* 技术栈的成熟度(使用人数,周边生态,仓库维护等)
		* 团队的熟悉度
	
```javascript
具体的技术选型
1. UI层: react
2. 路由层: react-router
3. 状态层: redux

// react项目结构
- public
	- mock  //  mock数据
		- products // 产品相关的mock数据
- src
	- componnets // 公共 ui组件
		- ErrToast // 全局的错误toast提示
			-index.js
			- style.css
	- containers // 容器组件, 一般是和router(页面)对应的. 
		-App // 入口文件, 引入路由, 全局处理的action
			- style.css
			- index.js
		- Home  // 引入该页面相关的action
			- index.js
			- style.css
			- components  // 该页面需要用到的组件
	- redux
		store.js   // root store, 创建store, 和使用一些中间件
		- modules  // sub reducer
			- entities  // 领域实体相关的reduce,比如商品, 店铺, 评论. 其它reducer可以通过id或者计算拿到相应的实体的信息
				- products.js
				- shops.js
				- comments.js
			- home.js  
			- detail.js
			- index.js  // root reducer  使用combineReducer进行合并
		- middleware   // 抽象出的一些中间件, 比如请求的处理
	- utils  // 工具库, 放置一些共用方法和请求的封装
	- images // 图片库
	- index.js  // 入口文件, 挂载store. 通过Provider组件注入全局store
- package.json
- .gitignore
- README.md


```

## Redux模块分层
```javascript
容器组件 => 页面状态,通用前端状态 => 领域状态(比如商铺,评论等)

// 网络请求层
1. 常规使用方式 redux-thunk.
2. 使用redux中间件进一步封装.

// 对整个项目进行分析, 对数据进行分类
// 根据数据的类别,抽象store
// 请求的封装和抽象(重复的代码,抽象出一个中间件)
// 抽象出一个通用的异常处理
```

#### 项目的构建和部署
* 部署到`nginx`静态服务器



#mydiary/javascript/react