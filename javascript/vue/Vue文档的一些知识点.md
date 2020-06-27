# Vue文档的一些知识点
#### 数据响应式的检测
* 数组: 一些直接改变数组的方法是可以检测到的, 而返回新数组的方法, 需要对该数据进行重新赋值
	* 直接改变数组的方法有: `push,pop,shift,unshift,splice,sort,reverse`
	* 返回新数组的方法有: `concat, filter, map, slice`总是返回一个新数组,这里就需要对数据进行重新赋值
	* 利用索引直接设置一个项时, 检测 不到 `vm.items[indexOfItem] = newValue`, 对于此种情况可以使用`splice或者vue.$set(obj, key, value)`
	* 修改数组的长度 `vm.items.length = newLength`,可以使用`splice`
* 对象:   `Vue`不能检测对象属性的添加和删除, 因此在给某一个对象增删属性时, 不会触发响应, 可以使用`vm.$set(vm.obj, key, value)`来触发

#### `vue`中对于使用较多的基础组件, 可以再`new Vue`之前, 全局注册到`vue`上

```javascript
import Vue from 'vue'
import upperFirst from 'lodash/upperFirst'
import camelCase from 'lodash/camelCase'

const requireComponent = require.context(
  // 其组件目录的相对路径
  './components',
  // 是否查询其子目录
  false,
  // 匹配基础组件文件名的正则表达式
  /Base[A-Z]\w+\.(vue|js)$/
)

requireComponent.keys().forEach(fileName => {
  // 获取组件配置
  const componentConfig = requireComponent(fileName)

  // 获取组件的 PascalCase 命名
  const componentName = upperFirst(
    camelCase(
      // 剥去文件名开头的 `./` 和结尾的扩展名
      fileName.replace(/^\.\/(.*)\.\w+$/, '$1')
    )
  )

  // 全局注册组件
  Vue.component(
    componentName,
    // 如果这个组件选项是通过 `export default` 导出的，
    // 那么就会优先使用 `.default`，
    // 否则回退到使用模块的根。
    componentConfig.default || componentConfig
  )
})
```

#### 异步组件

```JavaScript
// 配合webpack一起使用

// 第一种全局使用
Vue.component('async-webpack-example', function (resolve) {
  // 这个特殊的 `require` 语法将会告诉 webpack
  // 自动将你的构建代码切割成多个包，这些包
  // 会通过 Ajax 请求加载
  require(['./my-async-component'], resolve)
})

// 第二种使用promise

// 全局注册
Vue.component(
  'async-webpack-example',
  // 这个 `import` 函数会返回一个 `Promise` 对象。
  () => import('./my-async-component')
)
// 局部注册
new Vue({
  // ...
  components: {
    'my-component': () => import('./my-async-component')
  }
})

// 局部注册
const MyAsyncComponent = () => import('./my-async-component')
```

#### `vue`渲染的过程就是深度遍历的过程, 嵌套的组件遵循先子后父.

#### `vue`的生命周期: 
	* `beforeCreate`: `event和lifecycle`的初始化完成
	* `created`:  初始化`data, props, methods, wtach`完成
	* 基本上可以在`created`钩子函数里面访问到数据, 在`mounted`钩子函数里面可以访问到`DOM`, `在destory`里面可以做一些定时器销毁的工作
#mydiary/javascript/vue