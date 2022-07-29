# webpack
## eslint  
### eslint 安装
1. 安装eslint
``` javascript  
yarn add eslint --dev
```  
2. 创建配置文件 .eslintrc.{js,yml,json}  
``` javascript  
yarn create @eslint/config
```  
3. 在webpack中用eslint，还要安装 eslint-webpack-plugin插件
``` javascript
yarn add -D eslint-webpack-plugin
```  

### eslint 配置 （.eslintrc.js文件）  
``` javascript  
module.exports = {
  // 脚本设计在哪些环境中运行。每个环境都会带来一组预定义的全局变量
  env: {
    browser: true,
    es2021: true,
  },
  // 脚本在执行期间访问的其他全局变量。
  globals: {
    "var1": "writable",
    "var2": "readonly"
  },
  // 哪些第三方插件定义了ESLint要使用的其他规则、环境、配置等
  // https://eslint.org/docs/user-guide/configuring/plugins
  extends: 'eslint:recommended',
  // https://eslint.org/docs/user-guide/configuring/language-options#specifying-parser-options
  parserOptions: {
    ecmaVersion: 'latest',
    // set to "script" (default) or "module" if your code is in ECMAScript modules.
    sourceType: 'module',
  },
  // 启用哪些规则以及错误级别
  // https://eslint.org/docs/rules/
  rules: {},
};



// 禁止eslint规则：

/* eslint-disable no-alert, no-console */
alert('foo');
console.log('bar');


```  
## prettier
### prettier 安装
1. 安装  
``` javascript 
yarn add --dev --exact prettier
```  
2. 创建配置文件 .prettierrc
3. 创建忽略配置文件 .prettierignore  
``` javascript
# Ignore artifacts:
build
coverage

# Ignore all HTML files:
*.html
```  
4. 与linter集成 https://prettier.io/docs/en/integrating-with-linters.html  
5. 客户端命令： prettier --write . 

### prettier配置  
``` javascript 
// https://prettier.io/docs/en/options.html
{
  "semi": true,
  "singleQuote": true,
  "printWidth": 100
}
```  
### prettier cli运行 
```
prettier --write .
```  


## jest  
### 1. 安装  
``` javascript  
// install jest 
yarn add --dev jest
// install babel-jest  为了解析es6， jest不支持es6模块
yarn add --dev babel-jest @babel/core @babel/preset-env  
// jsdom
yarn add --dev jest-environment-jsdom
```  
### 2. 配置babel，在babel.config.js中  
``` javascript 
module.exports = {
  presets: [['@babel/preset-env', {targets: {node: 'current'}}]],
};
```  
### 3. 配置jest， 在 jest.config.js中  
``` javascript  
module.exports = {
    // 测试文件运行的环境 默认时node环境。
    // "testEnvironment": "jsdom",
    // 用于检测测试文件的文件路径
    "testRegex": "./.*/test/.*\\.test\\.(js|vue)$",
    //文件扩展名
    "moduleFileExtensions": [
      "js",
      "jsx"
    ],
    // 配置Jest来搜寻文件 
    // 告诉jest如何找到组件
    "moduleDirectories": [
      "node_modules",
      "src"
    ],
    //为特定的文件指定转换器或处理方式
    "transform": {
      "\\.[jt]sx?$": "babel-jest"
    },
    // 静态文件在测试中无足轻重，我们可以安全地mock他们  
    // <rootDir> 代表项目根目录。 大多数情况下，这代表你的package.json所在的位置
    "moduleNameMapper": {
      "\\.(css|less)$": "<rootDir>/__mocks__/styleMock.js",
      "\\.(gif|ttf|eot|svg)$": "<rootDir>/__mocks__/fileMock.js"
    },
    // Webpack的resolve.root、 NODE_PATH环境变量都可以使用 modulePaths选项。
    //"modulePaths": ["."],
    // collectCoverageFrom 数组来定义需要收集测试覆盖率信息的文件
    "collectCoverageFrom": [
      "**/src/**.{js,jsx}",
      "!**/node_modules/**",
      "!**/vendor/**"
    ],
    // 开启测试覆盖率
    "collectCoverage": true,
    // 开启测试覆盖率报告格式
    "coverageReporters": ["html", "text-summary"]
};
```  
### jest cli运行
``` 
    jest --coverage --config=jest.config.js
```  

## babel  
### 1. 安装  
``` javascript
/*
 @babel/core： babel翻译代码
 @babel/preset-env： 是一个智能预设，允许您使用最新的JavaScript，而无需微观管理目标环境需要哪些语法转换
*/
yarn add --dev babel-loader @babel/core @babel/preset-env 
// 以下两个可以避免辅助代码重复引入
//@babel/plugin-transform-runtime 一个插件，可以重复使用Babel的注入助手代码来节省代码大小。
yarn add --dev @babel/plugin-transform-runtime 
// @babel/runtime是一个库，包含babel模块化运行时助手和一个版本的再生器运行时。
yarn add @babel/runtime
``` 
### 2 配置babel, 在babel.config.js中
``` javascript 
module.exports ={
  "presets": [["@babel/preset-env", {
       /**
        * modules: "amd" | "umd" | "systemjs" | "commonjs" | "cjs"
        *  | "auto" | false, defaults to "auto".
        * 转换ES module 到 另一个模块类型。
        * cjs 是commonjs的别名 
        * false：保持ES格式  
        * 如果您使用的是Babel捆绑包，默认模块“auto”总是首选
       */
       "modules": false,
       // 该参数决定了我们项目需要适配到的环境
       "targets": {
          "chrome": "58" // 按自己需要填写
        },
        /**
         * 这个参数决定了 preset-env 如何处理 polyfills
         * 
         * false:  不推荐采用 false，这样会把所有的 polyfills 全部打入，造成包体积庞大  
         * 
         * usage:我们在项目的入口文件处不需要 import 对应的 polyfills 相关库。babel 会根据用户代码的使
         * 用情况，并根据 targets 自行注入相关 polyfills。
         * 
         * entry:我们在项目的入口文件处 import 对应的 polyfills 相关库此时， babel 会根据当前
         *  targets 描述，把需要的所有的 polyfills 全部引入到你的入口文件
         * （注意是全部，不管你是否有用到高级的 API）
        */
        "useBuiltIns": "usage",
        /**
         * 简单讲 corejs-2 不会维护了，所有浏览器新 feature 的
         *  polyfill 都会维护在 corejs-3 上。
         * 用 corejs-3，开启 proposals: true，proposals 为真那样我们
         * 就可以使用 proposals 阶段的 API 了。
         * 
        */
        "corejs": {
          "version": 3,
          "proposals": true
        }
    }
    ]],
    //测试环境 中的babel配置
    "env": {
      "test": {
        "presets": ["@babel/preset-env"],
      }
    }
  }
```  
## webpack使用typescript  
### 安装 
``` javascript
// https://github.com/TypeStrong/ts-loader
yarn add typescript --dev
yarn add ts-loader --dev
```
### webpack 配置  
``` javascript
// 加extensions ，否则报找不到modules
  resolve: {
    // Field 'browser' doesn't contain a valid alias configuration
    // D:\xinjundong\project\src\tsfiles\mathFunc doesn't exist
    extensions: ['.ts', '...']
  },
// 配置loader
{
  test: /\.tsx?$/, 
  loader: "ts-loader"
},
```  
### tsconfig.json文件中指定了编译这个项目的根文件和编译选项  
``` javascript
// https://www.tslang.cn/docs/handbook/tsconfig-json.html
{
    /**
     * "files"指定一个包含相对或绝对文件路径的列表。 "include"
     * 和"exclude"属性指定一个文件glob匹配模式列表。
     * */ 
    "include": ["src/**/*.ts", "test/**/*.ts"],
    // 排除
    "exclude": ["node_modules"],
    // https://www.tslang.cn/docs/handbook/module-resolution.html ts解析模块
    // https://www.tslang.cn/docs/handbook/compiler-options.html  
    /**
     * 模块解析策略共有两种可用的模块解析策略：Node和Classic。 
     * 你可以使用 --moduleResolution标记来指定使用哪种模块解析策略。
     * 若未指定，那么在使用了 --module AMD | System | ES2015时的默认值为Classic，
     * 其它情况时则为Node。
     * 
    */
    "compilerOptions": {
        "sourceMap": true
    }
}
```  
### ts使用第三方库  
 [使用方式](https://www.tslang.cn/docs/handbook/declaration-files/consumption.html)  
 [查找ts第三方库](https://www.typescriptlang.org/dt/search?search=)  
 ### ts使用jquery  
 ``` javascript
 // 安装   声明文件不在库里的安装方式
  yarn add jquery
  yarn add @types/jquery --dev 

  yarn add lodash
  yarn add @types/lodash --dev

// 声明文件在库里的安装方式 
// https://www.tslang.cn/docs/handbook/declaration-files/publishing.html
yarn add cheerio
 ```  
 










