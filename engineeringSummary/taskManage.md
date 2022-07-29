# 任务管理  
## electron
### electron是什么  
Electron是一个使用 JavaScript、HTML 和 CSS 构建桌面应用程序的框架。 嵌入 Chromium 和 Node.js 到 二进制的 Electron 允许您保持一个 JavaScript 代码代码库并创建 在Windows上运行的跨平台应用 macOS和Linux——不需要本地开发 经验。
### electron入门
1. node环境  
2. electron package.json  
init初始化命令会提示您在项目初始化配置中设置一些值 为本教程的目的，有几条规则需要遵循：
entry point 应为 main.js.
author 与 description 可为任意值，但对于应用打包是必填项。
``` javascript
{
  "name": "task-manage",
  "version": "1.0.0",
  "description": "electron project",
  "main": "main.js",
  "scripts": {
    "start": "electron .",
    "build": "electron-packager . task-manage --out ./out --ignore=node_modules"
  },
  "author": "xjdong",
  "license": "ISC",
  "devDependencies": {
    "electron": "^16.0.4"
  },
}
```  
3. 安装electron  
```  javascript  
npm install --save-dev electron  
```  
4. 您的 package.json配置文件中的scripts字段下增加一条start命令：
``` javascript
{
  "scripts": {
    "start": "electron ."
  }
}
// 测试
npm start
```  
5. 在主进程中:  创建window， 加载页面， 加载渲染进程执行的代码  
``` javascript
function createWindow() {
  const win = new BrowserWindow({
    width: 1000,
    height: 800,
    titleBarOverlay: {
      color: "#2f3241",
      symbolColor: "#74b1be",
    },
    //fullscreen: true,
    title: '我的任务管理',
    // 渲染进程执行的代码
    webPreferences: {
      preload: path.join(__dirname, "./preload/preload.js"),
    },
  });
  //console.log(read());
  // 加载页面
  win.loadFile("./page/dist/index.html");
}
// 与渲染进程通信的代码 收信息
ipcMain.on('save', (event, data) => {
  console.log(data) // prints "ping"
  save(data, function(res) {
    // 发信息
    event.reply('save-relay', res);
  })
  
})

app.whenReady().then(() => {
  createWindow();
  app.on("activate", function () {
    if (BrowserWindow.getAllWindows().length === 0) createWindow();
  });
});
```  
6. 在渲染器进程中： Preload 脚本，
``` javascript
//在渲染进程
const {contextBridge , ipcRenderer} = require('electron');
const {read} = require('../back/oprFile.js');

let saveCallback = function() {};
// 与主线程通信 收信息
ipcRenderer.on('save-relay', (event, arg) => {
  saveCallback(arg);
})
function saveData(data, callback) {
    saveCallback = callback;
    // 与主线程通信 发信息
    ipcRenderer.send('save', data);
}

// 向页面window暴露API接口
contextBridge.exposeInMainWorld('API', {
  read: () => read(),
  save: (data, cb) => {saveData(data, cb)}
})
```  
### electron 构建工具：  Electron Packager

1. 安装 electron-packager
``` javascript
npm install --save-dev electron-packager
```  
2. 命令介绍
npx electron-packager <sourcedir> <appname> --platform=<platform> --arch=<arch> [optional flags...]
``` javascript
"build": "electron-packager . task-manage --out ./out --ignore=node_modules"
```  
3. 参考 [electron-packager地址](https://github.com/electron/electron-packager)
      


### ant-design-vue 使用
``` javascript
import { createApp } from 'vue'
import { router } from './router/index.js'
import { store } from './store/index.js'
import App from './App.vue'
import Antd from 'ant-design-vue'
//样式
import 'ant-design-vue/dist/antd.css'
let app = createApp(App);
app.use(router);
app.use(store);
// 注册组件
app.use(Antd);
app.mount('#app')

```  


### vscode debug 配置  
1. 到vscode的debug视图  
2. 创建launch.js 文件
3. launch.js属性介绍
``` javascript  
{
  "version": "0.2.0",
  "configurations": [
    {
      // 调试语言类型
      "type": "node",
      // 当程序启动后立即停止
      "stopOnEntry": true,
      // 启动类型
      "request": "launch",
      // 调试器名称 可以单独启动这个调试配置
      "name": "Main",
      // 要使用的运行时可执行文件的绝对路径。默认值为node。请参阅“npm”和其他工具的启动配置支持部分。
      "runtimeExecutable": "${workspaceFolder}/project/node_modules/.bin/electron",
      //传递给运行时可执行文件的可选参数。
      "runtimeArgs": ["--remote-debugging-port=9223", "."],
      // 某一个平台的命令设置
      "windows": {
        "runtimeExecutable": "${workspaceFolder}/project/node_modules/.bin/electron.cmd"
      },
      //program: ''   启动调试器时要运行的可执行文件或文件
      //用于查找依赖项和其他文件的当前工作目录
      "cwd": "${workspaceFolder}/project"
    },
    {
      "name": "Renderer",
      "type": "chrome",
      "request": "attach",
      // 连接到正在运行的进程时的端口
      // 要使用的调试端口。请参见“附加到节点”部分
      "port": 9223,
      // 他是你源代码的根目录。通常，默认情况下，webRoot是您的工作区文件夹。此选项用于源地图分辨率。
      "webRoot": "${workspaceFolder}/project/page/dist/"
    }
  ],
  // 组合调试配置信息
  "compounds": [
    {
      "name": "All",
      "configurations": ["Main", "Renderer"]
    }
  ]
}

```  
4. [参考链接](https://code.visualstudio.com/docs/editor/debugging)