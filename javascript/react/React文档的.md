# React文档的
#### 属性和`html`不一样的
* `className, htmlFor`

#### `state`

> 代表`UI`的完整且最小状态集合  

#### `react`事件绑定要写成驼峰的形式而非`html`的小写

#### `React-router-dom`
* 

#### `React`生命周期图


![](React%E6%96%87%E6%A1%A3%E7%9A%84/WechatIMG10841.png)

#### 开发`react`使用的一些库
* `redux`的工作流程
![](React%E6%96%87%E6%A1%A3%E7%9A%84/redux-workflow.png)

* `redux` : 状态管理
	* `react-redux`: `Provider和connect`使得组件使用`store`更加方便
	* `redux-thunk`: 处理异步`action`,  可以`dispatch`一个方法
	* ` redux-saga`: 处理异步`action`, 把异步的`action`抽到一个单独的文件(使用`generator`的写法)
	* `immutable`:  管理`state`
	* ` redux-immutable`: 合并子`store`里面的数据
* `react-transition-group`: 动画
* `styled-components`: `js写css`

### React配置typescript
* 初始化: 通过`yarn add react-app projectName --typescript 或者 npx create-react-app projectName --typescript`来初始化项目
* 添加`tslint`: ` yarn add -D tslint tslint-config-prettier tslint-config-standard tslint-react`
	* 初始化`tslint`: `npx tslint --init`
	
```javascript
{

    "defaultSeverity": "error",

    "extends": [

        "tslint:recommended",

        "tslint-config-standard",

        "tslint-react",

        "tslint-config-prettier"

    ],

    "jsRules": {},

    "rules": {},

    "rulesDirectory": []

}

```

* 配置`redux和react-redux`: `yarn add redux react-redux @types/react-redux --dev`
* `create-react-app`里面默认集成了`css module`,可以直接使用
```javascript
// 配置sass
yarn add node-sass | npm install node-sass -D


// css 文件必须是 XX.module.scss 才会被编译
```

* 安装`react-router-dom`
```javascript

yarn add react-router-dom @types/react-router-dom
```
#mydiary/javascript/react
