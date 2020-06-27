## 如何使用webpack构建自己的项目
> web pack 是一个模块打包器  

#### 安装
* 不推荐全局安装`webpack`. 每个项目可能使用的webpack的版本号不一样,最好局部安装.

* `npm install module -D | —save—dev: 安装到devDependencies `
* `npm install module -S | —save: 安装到 dependencies`
* `npm webpack —config 指定的打包文件`
* `Webpack-cli`: 让我们可以在命令行中使用`webpack `这个指令

#### 打包的信息:
	* `hash`: 打包的唯一`hash`值
	* `version`: `webpack `版本号
	* `time`:打包的总耗时
	* `Build at`: 打包的时间
	* `chunks`: 模块的id值
	* `chunk names`: 模块的名称

#### `entry: str | object`: 
* `str`: 打包的入口文件
* `object: {main: entry-file, sub: entry-file}`: 可以配置多个入口文件

#### `output`: 输出配置
* `filename`: 输出的文件的名称,可以使用一些占位符
	* `[hash] | [name] | [id] | [chunkhash] 等`
* `path`: 输出的文件路径
* `publicPath`:  对于按需加载或加载外部资源是一个很重要的配置项.

#### `loader`

> 执行顺序: 从下到上,从右到左.  
* `test`: 匹配规则,是一正则
* `use`:  要使用什么`loader`进行解析
	* 可以为`loader`添加配置项

```javascript
module.exports = {
	module: {
		rules: [
			{
				test: /\.(png|jpg|gif)/,
				use: [
					{
						loader: 'file-loader',  
						options: {
              			name: '[path][name].[ext]',
            			},
					}
					
				]
			}
		]
	}
}
```

#### `file-loader`
* 用来打包图片文件等静态资源,也可以用来打包字体文件.
* 可以添加配置项
	* `outputPath`:  打包的文件路径
	* `pulicPath`:
	* `name`: `'[name]-[hash].[ext]'`
#### `url-loader`
* 功能比较强大,可以做`file-loader`做的,还可以把一定范围内的图片转成`base64`
* 支持`file-load`的配置项
* `limit`: 文件的大小限制. 小于这个限制则转成`base64`, 大于则用`file-loader`处理

#### `style-loader,css-loader,sass-loader,postcss-loader`

* `style-loader`: 
* `css-loader`:  可以配置`module`来支持模块化.`importLoaders: 2`来支持`@import`引进的样式文件,也需要经过其它的几个loader处理后, 才用`css-loader`进行处理
* `sass-loader`: 编译 `scss`文件
* `postcss-loader`: 用来加前缀和编译

#### `file-loader` 处理字体文件

### `Plugins`
> 可以再`webpack`运行到某个时候的时候, 做某一件事情.  
* `html-webpack-plugin`:  会在打包结束后,自动生成一个`html`文件,并把打包生成的`js`自动引入到这个`html`文件中
* `clean-webpack-plugin`: 打包时, 删除之前打包好的文件

#### `sourceMap` : 可以将编译后的代码映射回原始源代码, 代码出错,便于跟踪和调试.
* `cheap-source-map`: 打包后的代码, 追踪到行. 会忽略其它模块的跟踪
* `eval`: 打包后的代码
* `source-map`:
* `cheap-module-eval-source-map`:  会追踪到原始源代码, 到行. 开发环境可以配置这个
* `cheap-module-source-map`:
* 生产环境, 不生成`source map`, 是一个不错的选择

#### `webpack-dev-server:  object`
* `contentBase: filePath`  告诉服务器从哪个目录中提供内容.
* `publicPath`: jj
* `open: boolean`: 是否自动打开浏览器
* `compress: boolean`: 是否采用`gzip`压缩
* `after:function(arr.server){}`: 再服务内部的所有其他中间件之后,提供执行自定义中间件的功能.
* `before: function(app, server){}`: 再服务内部的所有其他中间件之前,提供执行自定义中间件的功能. 可以再这里发送一些请求,进行`mock`数据,或者转发请求解决跨域的问题
* `filename`: 在`lazy mode`惰性模式中,此选项可减少编译
* `headers`:  再所有响应中添加首部内容
* `host, port`: 配置域名和端口
* `https: boolean | options`: 是否启用`https`或者配置`https`的证书 
* `openPage: filePath`: 指定打开导航页面
* `proxy: object | function`:  服务代理
	
```javascript
module.exports = {
	devServer: {
		proxy: {
			'/api': 'http://localhost:3000'
		}
	}
}
```
#### 用`node`写一个自己的`server`, 再node中使用`webpack`

#### `hot-module-replace-plugin`: 热更新. 方便开发,修改`css或者js`不会刷新页面

#### `babel`:  编译`js`的一个库
* 下面使用来编译ec6的

```javascript
	// webpack.config.js
// npm install --save-dev babel-loader @babel/core
// npm install --save @babel/polyfill
module.exports = {
	module: {
		rules: [
			{
        		test: /\.js$/,
        		exclude: /node_modules/,
        		loader: 'babel-loader'
			}
		]
	}
}

// .babelrc

{
  "presets": [
    [
      "@babel/preset-env",
      {
			        //  useBuiltIns: 'usage' // npm i -S core-js@2.5.7, 否则报错

        "useBuiltIns": "entry"
      }
    ]
  ]
}
```

* 配置打包react的可以根据 babel 文档 [@babel/polyfill · Babel](https://babeljs.io/docs/en/babel-polyfill), 中的`presets`中查看 `preset-react`

#### `Tree Shaking` 只支持 `es Module`(静态引入, `import`)

* 通常用于描述移除`javasctipt`上下文中未引用代码
* `webpack 4.0`生产模式默认带有该功能
* 开发环境可以配置下
```javascript
module.exports = {
	mode: 'development',
 	optimization: {
    usedExports: true
  }
}

// package.json

{
	  "setEffects": [
   	 "*.css"
  		]
}
```

#### `Development和Production`模式的区分打包

* `production`: 代码时被压缩的
* `development`: 
* 
#### `code splitting`: 代码分割 
* 代码分割和`webpack`无关
* webpack实现代码分割俩种方式 
	* 同步代码: 只需要在在`webpack`的配置项中做`optimization`的配置即可. 防止重复打包
		* `splitChunks:   chunks: “all” }`
	*  异步代码(`import`): 异步代码,无需做任何配置,会自动进行代码的拆分
		* `babel-plugin-dynamic-import-webpack`需要这个插件 
#### `lazy loading`和懒加载

#### 打包分析, `Preloading, Prefetching`

* 两者有很多不同之处
	* `preload chunk`会在父`chunk`加载时,以并行方式开始加载. `prefetch chunk`会在父`chunk`记载结束后开始加载
	* `reload chunk`具有中等优先级,并立即下载. `prefetch chunk`再浏览器闲置时下载
	* `preload chunk`会在父`chunk`中立即请求,用于当下时刻. `orefetch chunk`会用于未来的某个时刻.
	* 浏览器支持程度不同
```javascript
// 再需要异步加载的文件前面这句注释就可以
import(/* webpackPrefetch: true */ 'LoginModal');

```

#### 缓存
* 修改`output`输出的文件的名称.
	* `['contenthash']`: 文件没有做修改的时候,不变

#### 垫片: `shiming`预置依赖
* 模块下开发, 一些第三方库暴露出的变量. 其他模块访问不到. 可以通过垫片,预置一些全局变量依赖
```javascript
const webpack = require('webpack')
module.exports = {
	plugins: [
		new webpack.ProviderPlugin({
			_: 'lodash',
			join: ['lodash', 'join']
		})
	]
}
```
#### 环境变量:
* 可以再命令行中注入环境变量. 以便打包的时候,知晓当前的打包环境

```javascript
// package.json

{
	scripts: {
		build: "webpack --env.production --config ./build/webpack.common.js"
// build: "webpack --env.production=xxx --config ./build/webpack.common.js"
// build: "webpack --env production --config ./build/webpack.common.js"
	}
}

// webpack.common.js

module.exports = (env) => {
// 生产环境
	if(env.production) {

	} else {
// 其他环境
	}
}
```

#### 如何打包一个库文件
```javascript
const path = require('path')
module.exports = {
	entry: './src/index.js',
	output: {
		path: path.resolve(__dirname, 'dist'),
		filename: '[name].js',
		library: 'MyLibrary', // 
		libraryTarget: 'this',
	},
	// 如果库中引入第三方的库时, 需要配置下. 
	// 防止项目中引用时,打包多次 
  externals: {
    jquery: {
			commonjs: 'jquery',
			root: '$'
		}
  }
}
```

#### PWA: Progressive Web App
* 可靠 - 即使在不稳定的网络环境下，也能瞬间加载并展现
* 体验 - 快速响应，并且有平滑的动画响应用户的操作
* 粘性 - 像设备上的原生应用，具有沉浸式的用户体验，用户可以添加到桌面

#### TypeScript 配置

```javascript
// 需要安装 ts-loader typescript
// tsconfig.json
{
  "compilerOptions": {
      "outDir": "./dist",
      "allowJs": true,
      "target": "es5",
      "module": "es6"
  },
  "include": [
      "./src/**/*"
  ],
  "exclude": ["./node_modules"]
}
// webpack.config.js

module.exports = {
	entry: './src/index.ts',
	module: {
		rules: [
			{
				test: /\.tsx?$/,
				loader: 'ts-loader'
			}
		]
	}
}


// 安装第三方库  npm install @types/xxx -D
npm install @types/lodash -D
```

#### 用`webpack-dev-server`开做代理
* 详细的配置可以看[开发中 proxy 配置server(devServer)](https://webpack.docschina.org/configuration/dev-server/#devserver-proxy)

```javascript
	module.exports = {
		devServer: {
    		contentBase: path.join(__dirname, 'build'),
    		compress: true,
    		open: true,
    		port: '3000',
    		hot: true,
    		hotOnly: true,
   		 proxy: {
     			 // '/react/api':'http://www.dell-lee.com'
      		'/react/api': {
       			 target: 'http://www.dell-lee.com',
       			 // pathRewrite: {
       			 //   'header.json': 'demo.json'
        			// }
      }
    }
  }

	}
```

#### 配置`eslint`
* `vue`和`react`都有自己的脚手架,可以很容易的配置.
* 相关的插件有`babel-eslint eslint-loader`

#### `webpack` 性能优化
* 跟上技术的迭代(`Node,Npm,Yarn`)
* 在尽可能少的模块上应用`loader`
	* 可以使用`include和exclude`缩小`loader`的使用范围
* `plugin`尽可能精简并确保可靠
* `resolve`: 配置模块如何被解析
	* `alias`:  创建`import`或`require`的别名,来确保模块引入变得更简单
	* `extentsions`:  自动解析确定的扩展
	* `mainFiles`: 解析目录时要使用的文件名
* 控制包文件的大小
* 通过`DllPlugin && DllReferencePlugin`拆分`chunks`.
* `thread-loader, parallel-webapck,happypack`多进程打包
* 合理使用`sourceMap`
* 结合`stats`分析打包结果
* 开发环境内存编译
* 开发环境无用插件剔除

```JavaScript
module.exports = {
	resolve: {
		extensions: ['js','jsx'],
		alias: {
			'@src': path.resolve(__dirname,'../src/components/')
		},
		mainFiles: ['build', 'src', 'utils']
	
	}
}
```

#### DllPlugin 和 DllReferencePlugin
> 拆分`bundles`, 提高构建的速度  

```javascript
// webpack.dll.js
module.exports = {
  mode: 'production',
  entry: {
    lodash: ['lodash'],
  },
  output: {
    filename: '[name].dll.js',
    path: path.resolve(__dirname, '../dll'),
    library: '[name]'
  },
  plugins: [
    new webpack.DllPlugin({
      name: '[name]',
      path: path.resolve(__dirname,'../dll/[name].manifest.json')
    })
  ]
}

// webpack.config.js

module.exports = {
	plugins: [
		new AddAssetHtmlWebpackPlugin({
        filepath: path.resolve(__dirname, '../dll/lodash.js')
      }),
		new webpack.DllReferencePlugin({
        manifest: path.resolve(__dirname, '../dll/lodash.manifest.json')
      })
	]	
}
```

#### 多页面的打包
* 需要再`entry`里面配置几个入口
* `html-webpack-plugin`根据`enter`的入口生成对应的`html`

``` javascript
module.exports = {
	entry: {
		index: './src/index.js',
		main: './src/main.js'
	},
	plugins: [
		new HtmlWebpackPlugin({
			templete: './src/index.html',
			filenmae: 'index.html',
			chunks: ['lodash','index']
		}),
		new HtmlWebpackPlugin({
			templete: './src/index.html',
			filenmae: 'list.html',
			chunks: ['lodash','list']
		}),
	]
}

// 也可以根据config里面的entry来动态生成
const makePlugin = (config) => {
	const{ entry, plugins} = config.entry

	if(typeof entry === 'string') {
		const name = /[\w|./]?(\w+).jsx?$/
		return plugins.push(
			new HtmlWebpackPlugin({
			templete: './src/index.html',
			filename: 'list.html',
			chunks: ['lodash','list']
			})
		)
	} 
	if(typeof entry === 'object') {
		return Object.keys(entry).map((item) => {
			plugins.push(
				new HtmlWebpackPlugin({
					templete: './src/index.html',
					filename: `${item}.html`,
					chunks: ['lodash',list]
			})

			)
		})
	}
	return config.entry
}

```


* 给一棵二叉树 和 一个值，检查二叉树中的是否存在一条路径，这条路径上所有节点的值加起来等于给的那个初始值。例如，对于下面的二叉树，如果初始值是 22，那么存在一条路径 5->4->11->2

#mydiary/webpack
