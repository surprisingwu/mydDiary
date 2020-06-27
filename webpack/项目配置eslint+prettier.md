# 项目配置eslint+prettier
#### 引入`eslint prettier lint-staged`的原因
* 代码规范落地难. 
* 低质量代码带入线上应用
* 代码格式难统一
* 代码质量文化难落地

#### 配置`eslint`
* 这里使用`eslint-cli`来初始化项目
		* `npx eslint --init`会进入一系列的选项,和`vue-cli`类似. 在选择标准规范的时候, 我选择的是`standard`格式.(`airbnb`检查太过严格, 现在项目也不是项目初期, 改动太多,太大)
* 在`.eslintrc.js`中新增`wx`全局变量

```
globals: {
    Atomics: 'readonly',
    SharedArrayBuffer: 'readonly',
    wx: 'writable'  // 新增该配置
  }
```

* `vue.config.js`新增`loader`规则
		* 前面的配置,只是在开发中会有提示, 但是开发者可以忽略.所以新增编译的异常提示,提醒开发者,去修改
		* 配置我这里只检查了`.js`结尾的文件. 以`.vue`文件结尾的文件太多. 以后开发的时候, 慢慢修改.

    
```js
{
devServer: {
    overlay: {
      warnings: true,
      errors: true,
    },
  },
configureWebpack: {
    module: {
      rules: [
        {
          // test: /\.(js|vue)$/, // vue 文件太多, 修改麻烦. 以后开发规范下
          test: /\.js$/,
          enforce: 'pre',
          include: path.resolve(__dirname, 'src'),
          exclude: /node_modules|dist/,
          loader: 'eslint-loader',
          options: {
            fix: true,
          },
        },
      ],
    }
  }
  }
```
#### 配置`prettier lint-staged husky`
* 安装插件
		* `npm install -D prettier husky lint-staged eslint-config-prettier eslint-plugin-prettier`
		* `eslint-config-prettier`: `eslint`和`prettier`的校验规则冲突时,以`prettier`为准
		* `eslint-plugin-prettier`: 结合`eslint`使用的插件
		* `lint-staged`: 该插件可以执行一系列任务, 但是只对`git add `后的文件进行检查和修复
		* `husky`: 会执行`git hook`函数, 这里用到的是`precommit`
* 修改`package.json`
		* `scripts`:
				* `format`: 可以启动改指令对指定的文件夹进行校验
		* `lint-staged`
				* `src/**/*.{js,json,vue}`: 将要进行任务的文件夹
				* 键值: 对修改的文件执行的一系列任务
						* `eslint --fix --ext .js,.vue`: 使用`eslint`校验修改的文件, 并进行修复
						* `prettier --write`: 使用`prettier`对修改的文件,进行修复
						* `git add`: 前面的任务执行完,没有报错,执行该命令
		* `husky`: `git hook`执行时的要执行的命令

```javascript
  {
        "scripts": {
             "format": "prettier --write \"src/**/*.js\" \"src/**/*.vue\"",
        },
        "lint-staged": {
            "src/**/*.{js,json,vue}": [
                "eslint --fix --ext .js,.vue",
                "prettier --write",
                 "git add"
             ]
    },
      "husky": {
        "hooks": {
          "pre-commit": "lint-staged"
        }
  },
}
```
 
	* `.eslintrc.js`的相关配置如下

```javascript
module.exports = {
  env: {
      browser: true,
      es6: true
  },
  extends: [
      'plugin:vue/essential',
      'standard',
      'plugin:prettier/recommended'
   ],
  globals: {
      Atomics: 'readonly',
      SharedArrayBuffer: 'readonly',
      wx: 'writable',
      uni: 'writable'
  },
  parserOptions: {
      ecmaVersion: 2018,
      sourceType: 'module'
  },
  plugins: [
      'vue'
  ],
    rules: {
      "prettier/prettier": "error"
  }
}

```    

		* 添加`prettier.config.js`
				* 规范可以讨论下,采用什么风格的.

```javascript
module.exports = {
    printWidth: 120, // 每行代码长度（默认80）
    tabWidth: 4, // 每个tab相当于多少个空格（默认2）
    useTabs: true, // 是否使用tab进行缩进（默认false）
    singleQuote: true, // 使用单引号（默认false）
    semi: false, // 声明结尾使用分号(默认true)
    trailingComma: 'all', // 多行使用拖尾逗号（默认none）
    bracketSpacing: true, // 对象字面量的大括号间使用空格（默认true）
    arrowParens: 'avoid', // 只有一个参数的箭头函数的参数是否带圆括号（默认    avoid）
    proseWrap: 'always',
}

```
    


####     `vscode`相关配置

		* 可以配置`vscode`相关设置, 再保存时,进行自动修复.
		* 配置如下

```javascript
{
"eslint.validate": [
      "javascript",
      "javascriptreact",
      {
        "language": "html",
        "autoFix": true
      },
      {
        "language": "vue",
        "autoFix": true
      }
    ],
    "eslint.autoFixOnSave": true,

}

```
    
*  参考文档
		*  [vue.config.js](https://cli.vuejs.org/config/)
		*  [eslint](http://eslint.cn/docs/user-guide/configuring)
		*  [prettier](https://prettier.io/docs/en/cli.html)
		*  [lint-staged](https://github.com/okonet/lint-staged)
		*  [husky](https://github.com/typicode/husky#upgrading-from-014)
   
#mydiary/webpack